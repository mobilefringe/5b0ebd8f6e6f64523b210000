<template>
	<div class="row page_content" v-if="dataLoaded">
		<div class="banner_div">
			<div class="home-banner-container">
				<slick ref="slick" :options="slickOptions">
					<div class="" v-for="banner in banners" v-if="banners">
					    <!--<div class="home-banner" v-bind:style="{ backgroundImage: 'url(' + banner.image_url + ')' }"></div>-->
						<div class="home-banner" v-lazy:background-image="banner.image_url">
						    <!--<div class="site_container">-->
						        <div class="banner_text site_container">
						            <div class="banner_title_container">
                                        <h2 class="banner_title" v-if="banner.name">{{banner.name}}</h2>
                                    </div>
                                    <router-link class="banner_btn hvr-icon-wobble-horizontal caps" :to="banner.url" v-if="banner.description" >{{banner.description}} <i class="fa fa-angle-right hvr-icon"></i></router-link>
                                </div>
						    <!--</div>-->
						</div>
					</div>
				</slick>
			</div>
		</div>
		<div class="site_container">
		    <div>
		      <h4 class="home_page_title caps">{{$t("home_page.explore")}} {{property.name}}</h4>
		    </div>
		    <div class="feature_items_container clearfix">
		        <div class="feature_item col-sm-4"  v-if="feature_items" v-for="feature in feature_items">
		            <a :href="feature.url" target="_blank" v-if="feature.do_anchor_tag">
    		            <img :src="feature.image_url" :alt="feature.name" class="image">
    		            <div class="feature_name">
    		                {{feature.name}}
    		            </div>
		            </a>
    		        <router-link :to="feature.url" v-else>
    		            <img :src="feature.image_url" :alt="feature.name" class="image">
    		            <div class="feature_name">
    		                {{feature.name}}
    		            </div>
		            </router-link>
		        </div>
		    </div>
		    <div>
		        <div class="visible_phone">
		            <div class="google_map">
            		    <iframe src="https://www.google.com/maps/embed?pb=!1m14!1m8!1m3!1d3238.228400622835!2d-79.70550983095802!3d43.41266509546466!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x0%3A0xc115e4093fc343b7!2sSouth+Oakville+Centre!5e0!3m2!1sen!2sca!4v1524581405558" width="100%" height="375" frameborder="0" style="border:0" allowfullscreen></iframe>
            		</div>
		        </div>
    		    <div class="col-sm-4">
    		        <div v-if="property" class="property_dets">
						<p class="property_name caps">{{property.name}}</p>
						<p>{{property.address1}}</p>
						<p>{{property.city}}, {{property.province_state}}, {{property.postal_code}}, {{property.country}}</p>
						<p class="property_phone">{{property.contact_phone}}</p>
					</div>
					<div class="home_contact_container">
						<div class="description_text text-left caps contact_us_dets">
							CONTACT US
						</div>
						<form class="form-horizontal" action="form-submit" @submit.prevent="validateBeforeSubmit">
							<div class="form-group ">
								<div class="col-sm-12" :class="{'has-error': errors.has('email')}">
									<label class="label" for="email">Your Email</label>
									<input v-model="form_data.email" v-validate="'required|email'" class="form-control" :class="{'input': true}" name="email" type="email" placeholder="Email" data-vv-delay="500">
									<span v-show="errors.has('email')" class="form-control-feedback">{{ errors.first('email') }}</span>
								</div>
								<div class="col-xs-12" :class="{'has-error': errors.has('message')}">
									<label class="label" for="message">Your Message</label>
									<textarea v-model="form_data.message" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="message" type="text" placeholder="Message" data-vv-delay="500"></textarea>
									<span v-show="errors.has('message')" class="form-control-feedback">{{ errors.first('message') }}</span>
								</div>
							</div>
							<div class="form-group account-btn text-left m-t-10">
								<div class="col-xs-12 text-left">
									<button class="contact_btn hvr-grow-shadow " type="submit" :disabled="formSuccess"><span class="hidden_phone">Submit</span><span class="visible_phone">Send Message</span></button>
								</div>
							</div>
						</form>
						<div id="send_contact_success" class="alert alert-success text-left" role="alert" v-show="formSuccess">
							<span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
							<span class="sr-only">Success</span>
							Thank you for contacting us. A member from our team will contact you shortly.
						</div>
						<div id="send_contact_error" class="alert alert-danger text-left" role="alert" v-show="formError">
							<span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
							<span class="sr-only">Error:</span>
							There was an error when trying to submit your request. Please try again later.
						</div>
					</div>
    		    </div>
    		    <div class="col-sm-8 hidden_phone">
    		        <div class="google_map ">
            		    <google-map :property="property" key="1"></google-map>
            		</div>
    		    </div>
    		</div>
		</div>
	</div>
</template>

<script>
    define(["Vue", "vuex", "vue!today_hours", "vue!search-component", 'vue!vue-slick', 'js-cookie', 'vue-lazy-load', "vue!google_map",  'vee-validate', "utility"], function(Vue, Vuex, TodayHoursComponent, SearchComponent, slick, Cookies, VueLazyload, GoogleMapAPI, VeeValidate, Utility) {
        Vue.use(VueLazyload);
        Vue.use(VeeValidate);
        return Vue.component("home-component", {
            template: template, // the variable template will be injected
            props:['locale'],
            data: function() {
                return {
                    slickOptions: {
                        arrows: false,
                        autoplay: true,
                        autoplaySpeed: 6000,
                        cssEase: 'linear',
                        dots: true,
                        fade: true,
                        infinite: true,
                        slidesToShow: 1,
                        speed: 1600
                    },
                    dataLoaded: false,
                    form_data : {},
                    formSuccess : false,
                    formError: false,
                    instaFeed: null
                }
            },
            created () {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    
                    this.form_data.email = null;
                    this.form_data.subject = this.property.name + ' Contact Us Form';
                    this.form_data.message = null;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores',
                    'getTodayHours'
                ]),
                banners () {
                    // var banners = this.$store.state.banners;
                    // banners.map(banner => { 
                    //     banner.image_url = "//codecloud.cdn.speedyrails.net/sites/5b0ebd8f6e6f64523b210000/image/jpeg/1524085268825/HeroImage.jpg";
                    //     banner.name = this.property.name;
                    //     banner.description = "See Stores";
                    //     banner.url = "/stores";
                    // });
                    
                    // return _.orderBy(this.$store.state.banners, ['position'], ['asc']);
                    var banners = [];
                    _.each(_.range(b, end); , function (value, key) {
                        console.log(value, key);
                    });
                    return _.orderBy(banners, ['position'], ['asc']);
                },
                feature_items () {
                    // return this.$store.state.feature_items;
                    var features = this.$store.state.feature_items;
                    _.forEach(features, function(value, key) {
                      
                        if(value.name === null || value.name === undefined || value.name.length == 0) {
                            value.no_hover_class = false;
                        }
                        else {
                            value.no_hover_class = true;
                        }
                        if( _.includes(value.url, '//')) {
                            value.do_anchor_tag = true;
                        }
                        else {
                            value.do_anchor_tag = false;
                        }
                    });
                    return _.slice(features, [0], [6]);
                },
                full_address() {
                    return this.property.address1 + '' + this.property.city + '' + this.property.country + '' + this.property.province_state + '' + this.property.province_state

                }
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "banners"), this.$store.dispatch("getData", "feature_items"), this.$store.dispatch("getData", "promotions"), this.$store.dispatch("getData", "popups"), this.$store.dispatch('LOAD_PAGE_DATA', {url: "http://bonniedoon.mallmaverick.com/api/v2/bonniedoon/social.json"})]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                closePopup() {
                    this.show_popup = false;
                    document.getElementById('popup_backdrop').style.display = "none";
                },
                validateBeforeSubmit() {
                    this.$validator.validateAll().then((result) => {
                        let errors = this.errors;
                        send_data = {};
                        send_data.form_data = JSON.stringify(Utility.serializeObject(this.form_data));
                        this.$store.dispatch("CONTACT_US", send_data).then(res => {
                            this.formSuccess = true;
                        }).catch(error => {
                            try {
                                if (error.response.status == 401) {
                                    console.log("Data load error: " + error.message);
                                    this.formError = true;
                                } 
                                else {
                                    console.log("Data load error: " + error.message);
                                    this.formError = true;
                                }
                            } 
                            catch (e) {
                                console.log("Data load error: " + error.message);
                                this.formError = true;
                            }
                        })
                    })
                },
            }
        })
    })
</script>
