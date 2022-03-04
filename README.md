# Backend Technical Test

We are a company selling software licence keys and we have an API that allows users to:

- Create user accounts (POST /registrations)
- Subscribe to plans that has a limited duration in time (POST /plans/{id}/subscriptions)
- Cancel plan subscription (DELETE /users/me/subscription)

We want to build an event system that allows us to run arbitrary chuncks of code regarding these events.
It's mostly to send emails but this can be anything (giving a refund, updating records, etc.)

Our boss ALWAYS changes his mind.
So we need a system that is flexible enough to add and change events easily, almost without changing the implementation.

As you see, there's 4 possible events:

- User registration (`registration`),
- User subscribing to plan (`subscription`),
- Subscription reaches expiration date (`subscriptionExpiration`)
- User cancels it's subscription (subscriptionCanceled)

Dont give a damn about sending email and making payment operations. Use dummy objects with only `console.log`.
Make the most basic implementation of the API (no need for authentication/authorization)

**What we want to see is the schedule system implementation.**

## The code behind the event creation

```javascript
schedule(() => [
  {
    from: 'registration', action: (user) => {
      Mailer.sendWelcomeMessage(user)
    },
    from: 'registration', duration: 3, 'days', action: () => {
      Mailer.wouldYouBeInterestedInPaidPlan(user)
    },
    from: 'subscription', duration: 15, 'minutes', action: () => {
      Payment.debitCard(user)
      Mailer.hereIsYourLicenceCode(
        user.generateLicenceCode()
      )
    },
    from: 'subscriptionExpiration', duration: -3, 'days', action: () => {
      Mailer.yourLicenceIsAboutToExpire(user)
    },
    from: 'subscriptionExpiration', action: (user) => {
      user.markLicenceAsExpired()
      Mailer.yourLicenceIsDead(user)
    },
    from: 'subscriptionExpiration', duration: 3, 'weeks', action: (user) => {
      Mailer.weMissYou(user)
    },
    from: 'cancelSubscription', action: (user) => {
      Payment.giveProrataRefund(user)
      Mailer.sorryToSeeYouLeave(user)
    },
    // This event runs each year
    from: 'registration', duration: '*', 'year', action: () => {
      Mailer.happyAnniversary(user)
    },
  }
])
```

## Requirements

- The work should be implemented using Javascript; you can use some frameworks/microframeworks like expressjs). 
- The database should be PostgreSQL, MySQL, or SQLite.
- The work should be covered with unit tests.
- Adding a new **event types/events** should be as easy as possible.

## Deliverability

- The code should be delivered as a working REST API.
- The code should be delivered as a Git repository with an incremental set of meaningful commits and not only one. 
- Will be judged: the quality of the code, the quality of commits, the quality of the documentation (in and outside of code), capacity in asking for help, and the elegance of the solution.
- The point is not for you to figure things out from scratch if you have never done something similar. Feel free to ask for pointers and help when stuck.
- Bonus (not needed): deployed version of the API.
