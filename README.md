# SimpleNetworkHelper
This is simple lightwight http network call solution. it is build on top of couple of well known libraries(mainly [okhttp](https://github.com/square/okhttp)) and [RxAndroid](https://github.com/ReactiveX/RxAndroid).  

The API is Glide like and simple to use. The core feature is the convinience over response handling. the response handlers are completely decoupled from the networking and threding module. practically you can create and plug any kind of handler you want. 

lets check the example,

  ```java
   Subscription subscription =
                NetworkHelper
                        .getHelper()
                        .createRequest()
                        .get("http://www.bing.com/az/hprichbg/rb/Halloween2016_EN-US7682362704_1920x1080.jpg")
                        .with(ImageHandler.Builder.create(200, 200))
                        .subscribe(bitmap -> {
                            ((ImageView) findViewById(R.id.imageView)).setImageBitmap(bitmap);
                        });
  ```                        
the `with` method accepts a `HandlerBuilder` creates a the `Handler` on the fly and ommits a Observable. 
  ```java
  public <Z, T extends Handler<Z>> Observable<Z> with(final HandlerBuilder<T> t)
  ```
Json reponse sample, 

  ```java
    Subscription subscription = NetworkHelper
                .getHelper()
                .createRequest()
                .get(BING_TEST_URL)
                .with(JsonHandler.Builder.createDefault(BingDummyModel.class))
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(bingDummyModel -> {
                    Utils.logd("response " + bingDummyModel.images.get(0).url);
                });
  ```


Building a custom handler is pretty straight forward. just extend the `Handler<T>` definnation and write down your own logic to handle the response. create the HandlerBuilder by extending `HandlerBuilder<T>`.

The other part is that this mechanism will cache the service response in memory(RAM). it was just what i had to implement for a pertiular case. note that it isnt using okhttp caching mechanism which is way better aproach if you have more control over server response. it is also more standard practice that is followed. but in memory mechanism is certainly usefull is some case where you want to have more control over what is cached.     


