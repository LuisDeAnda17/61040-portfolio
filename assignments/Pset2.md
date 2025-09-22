# Problem Set 2

## Exercise 1: Concepts for URL Shortening

### Concept Questions:
1. Contexts help in differentiating shotertend URLs who may have the same nonce but the context seperates them. Contexts will typically be the domain name of our original URL, if not the base URL.

2. Storing a set of used nonces within each context helps avoid the repetition of the same nonce String within each context. The set of used strings is similar to a counter because each string used corresponding to a certain number in the counter, the nth string added. With strings, we have to generate a unique unused string, but with the counter, we could generate a function dependant on the counter that produces a unique nonce with each possible number.

3. Using common dictionary words would allow for users to more easily remember and likely type the new shortened URL; however, using real words creates the possibility that we run out of possible nonce Strings faster.

### Synchronizations for URL Shortening
1. This is to emphasize what parts/arguments of the when action(s) are actually necessary for the then action(s). In the first sync, the generate action does not need targetURL; however, register does. This exclusion in the first sync to avoid adding unnessecary information for the sync.

2. If the variable name is excluded in every case, then we would not be able to differentiate between the same argument type if a sync has two actions with the same argument type in the same section.

3. The third sync does not need request because to set an expiry you only need the ShortURL from register. You could put request in the when portion of the sync; however, it would be redundant as you need Request for register to be set off.

4. The first two syncs, generate and register, would experience a few changes. Generate would always be registering to the same shortURLBase and would realistically not need arguments anymore if we imply bit.ly is always used. Similarly, register would also not need the shortURLBase if bit.ly was implied to be the shortURLBase. Overall, our syncs could be simplier if bit.ly was our only domain name.

5. 
    __sync:__ removeSync  
    __when:__ ExpiringResource.expireResource(): (resource: Resource)  
    __then:__   
        URLShortening.lookup(resource: shortURL)  
        URLShortening.delete(resource)  

    removeSync should remove the shortURL link to the baseURL IF it expires. In addition, it checks if the resource is in our URL link library. This is just to help avoid deleting something that may not be in our library. I understand this may be redundant or unnecessary but thought it would be best to check.


### Extending the Design

1. 
    __concept:__ URLVisitCounting  
    __purpose:__ count the number of times a short URL is accessed  
    __principle:__ Increment a counter associated with a URL when it is visited if it is stil active  
    __state:__   
        - a set of shortURLs with  
            - a count Number   
            - a active Flag  
    __actions:__  
    >    - register (shortURL: String)  
            - __requires:__ a shortURL that is valid (exists) and NOT expired  
            - __effects:__ the shortURL has an associated counter, intialized to 0, and its active Flag is set to ACTIVE  
    >    - deactivate (shortURL: String)  
            - __requires:__ a shortURL with a counter  
            - __effects:__ the shortURLs counter is reset to 0 and its active flag is set to DEACTIVE  
    >    - increment (shortURL: String)  
            - __requires:__ a shortURL with a counter  
            - __effects:__ increments the count for shortURL by 1  
    >    - returnCount(shortURL): (count: Number)  
            - __requires:__ the shortURL exists and, if so, its active Flag is is set to ACTIVE.   
            - __effects:__ returns the associated shortURL's count   

    __concept:__ URLOwner  
    __purpose:__ Associate a shortURL to a user to provide the analytics correctly  
    __principle:__ each shortURL is associated with exactly one owner  
    __state:__  
    >    - a set of shortURLs with  
            - an owner User   
    __actions:__  
    >    - assign ( user: User, ShortURL: String)  
            - __requires:__ no previous ownership with the ShortURL  
            - __effects:__ associates ShortURL with user  
    >    - returnOwnership (shortURL: String): (user:User)  
            - __requires:__ the shortURL has an owner and exists  
            - __effects:__ returns the owner of the shortURL  
    >    - verify (user: User, shortURL: String): (verification: boolean)  
            - __requires:__ the shortURL has an owner and exists  
            - __effects:__ if the user is the associated owner of the shortURL return True; False otherwise.  

2. 
    __sync:__ associateOwner  
    __when:__   
        Request.shortenUrl(user, targetURL, shortURLBase)  
        UrlShortening.register (shortURLBase, targetURL): (shortUrl)  
    __then:__  
        URLOwner.assign(user, ShortURL)  

    __sync:__ VisitIncrementer   
    __when:__ URLShortening.lookup(shortURL)  
    __then:__ URLVisitCounting.increment(shortURL)  

    __sync:__ AnalyticsExamination  
    __when:__   
        Request.getAnalytics(user, shortURL)  
        URLOwner.verify(user, shortURL)  
    __then:__  
        URLVisitCounting.returnCount(shortURL): (count)  
        Response.analytics(shortURL, counts)  

    this requires that URLOwner.verify returns True and that the shortURL actually exists.  

3. 
    - Allowing users to choose their own short URLs  
    >    We'd have to modify the sync 'register' to check if the desired short url is available and if it is to pass it to the URLShortening.register instead of the nonce. Otherwise, it should deny this action and with an error messae, inform the user that the desired shortURL is (taken/in use).

    - Using the “word as nonce” strategy to generate more memorable short URLs  
    >    We could either have a new concept RandomWord that pulls a random True word from a list or Word Dictionary and have it be used instead of NonceGeneration, or we extend the NonceGeneration to do a similar action so either a random nonce can be generated or picked out of a Word list.

    - Including the target URL in analytics, so that lookups of different short URLs can be grouped together when they refer to the same target URL  
    >    If this assumes that the analytics are returned for the targetURL without checking ownership, then this could cause some issues of privacy. Making this a undesireable feature.  
    However, in the more likely case of this refering to grouping analytics of shortURLs that refer to the same BaseURL, then this is what should be done. We can extend URLOwner to keep track of a set of BaseURLs with a set of ShortURLs to help associate multiple ShortURLs to one baseURL. When a URLShort.register occurs, we create an association of shortURL to the BaseURL. This extension would allow us to both attain multiple ShortURLs to one targetURL and check for ownership to return the analytics associated with the user and their shortURLs of that BaseURL.  



    - Generate short URLs that are not easily guessed  
    >    This can be done by modifying the wording of the NonceGeneration to highlight that the use of randmonized or cryptography methods are used when generating the nonce. One can argue this relates more to the implementation of the concept, but it does impact how we can word our concept.


    - Supporting reporting of analytics to creators of short URLs who have not registered as user  
    >    This is not a desireable feature. This could potentially lead to info leaks and cause unintended damages. It adds a lot of complexity when it comes to unregistered users, likely requiring some kind of password to be capable of accessing the analytics which would just be another account concept. Overall, this concept just adds unnecessary complexity and could lead to hazardous data leaks of analytics.


