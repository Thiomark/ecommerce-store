<template>
    <div  class="reviews-container">
        <div class="buttons">
            <input type="button" @click="reviewSettings({event: 'deletAll'})" value="Delete All Reviews">
            <input type="button" value="Add Reviews">
        </div>
        <div class="review" v-if="allReviews">
            <ReviewTemplate 
                v-for="review in allReviews" 
                :key="review._id" 
                :name="review.name" 
                :date="review.createdAt"
                :review="review.review" 
                :stars="review.rating" 
                :modifyReview="true"
                :reviewID="review._id"
                @popupmenuevent="reviewSettings"
            />
        </div>
    </div>
</template>

<script>

    import ReviewService from '../../services/ReviewService'
    import {mapActions} from 'vuex'
    import ReviewTemplate from '../reviews/ReviewTemplate'

    export default {
        name: 'ReviewsContainer',
        components: {
            ReviewTemplate
        },
        data() {
            return {
                allReviews: []
            }
        },
        methods: {
            ...mapActions(['setLoadingPage', 'setRequestFeedBack']),
            async reviewSettings(event){
                if(event.event === "delete"){
                    try {
                        await ReviewService.delete(event.reviewID)
                        this.allReviews = this.allReviews.filter((review) => {
                            return review._id !== event.reviewID
                        })
                    } catch (error) {
                        this.setRequestFeedBack(error.response.data.error)
                    }
                }else if(event === "edit"){
                    console.log('edit')
                }else if(event.event === "deletAll"){
                    try {
                        this.setLoadingPage(true)
                        await ReviewService.delete('')
                        this.allReviews = []
                        this.setLoadingPage(false)
                    } catch (error) {
                        this.setLoadingPage(false)
                        this.setRequestFeedBack(error.response.data.error)
                    }
                }
            },
        },
        async created() {
            try {
                this.setLoadingPage(true)
                const response = await ReviewService.getAll()
                await this.setLoadingPage(false)
                this.allReviews = response.data.fetcheQuerys 
            } catch (error) {
                this.setRequestFeedBack(error.response.data.error)
            } 
        },
    }
</script>

<style scoped>

    @media (max-width: 600px){
        ul {
            display: none;
        }
    }

    .reviews-container {
        display: grid;
        grid-template-columns: 1fr;
        gap: 20px;
        padding: 1em 2em;
    }


    input[type="button"] {
        padding: .8em 2em;
        box-shadow: rgba(0, 0, 0, 0.024) 0px 2px 1px 0px inset;
        background-color: #3d3d3d;
        color: #777777;
        border: none;
        font-size: .7em;
        margin: .5em 0;
        border-radius: 5px;
        color: #f3f3f3;
        margin-right: 2em;
    }

    @media (min-width: 600px){
        ul {
        /* display: grid;
        grid-template-columns: 70px 170px auto 200px 80px;
        width: 100%;
        border-radius: 5px;
        padding: .7em 1em;
        background-color: rgb(250, 250, 250);
        -webkit-box-shadow: 0px 6px 5px -4px rgba(0,0,0,0.22); 
        box-shadow: 0px 6px 5px -4px rgba(0,0,0,0.22); */
        
        }

        h1 {
            font-family: 'Roboto', sans-serif;
            font-weight: 500;
            font-size: 13px;
            color: #1b1b1b;
        }

        .fa-caret-down {
            padding-left: .9em;
            cursor: pointer;
        }
    }

</style>