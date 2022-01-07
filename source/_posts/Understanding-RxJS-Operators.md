---
title: "Understanding RxJS Operators: forkJoin, zip, combineLatest, and withLatestFrom"
author: Jecelyn Yeen
date: 2020-09-21
source_url: https://www.digitalocean.com/community/tutorials/rxjs-operators-forkjoin-zip-combinelatest-withlatestfrom
category: Tutorial
tag:
  - RxJS
  - Angular
---

### Introduction

If you are confused about the differences between `forkJoin`, `zip`, `combineLatest`, and `withLatestFrom`, you are not alone.

> These four operators are what we know as `combination operators` - we use them when we need to **join information from multiple observables**.

This article will talk about the usage and differences between these four operators, so you know which one to use when the time comes.



## Setup

Imagine you are **printing t-shirts**. Ms. Color holds the `color` information, and Mr. Logo holds the `logo` information. Both of them will pick `color` and `logo` spontaneously. You will need to wait and combine these two pieces of information continuously in order to print t-shirts. Ms. Color and Mr. Logo represent two observables in our code - `color$` and `logo$`.

![you, ms. color & mr. logo](https://scotch-res.cloudinary.com/image/upload/q_auto:good,f_auto/media/272/qp101EyBRxabSGbqdDz2_10.png)

```typescript
// 0. Import Rxjs operators
import { forkJoin, zip, combineLatest, Subject } from 'rxjs';
import { withLatestFrom, take, first } from 'rxjs/operators';

// 1. Define shirt color and logo options
type Color = 'white' | 'green' | 'red' | 'blue';
type Logo = 'fish' | 'dog' | 'bird' | 'cow';

// 2. Create the two persons - color and logo observables,
// They will communicate with us later (when we subscribe)
const color$ = new Subject<Color>();
const logo$ = new Subject<Logo>();

// 3. We are ready to start printing shirts. You need to subscribe to color and logo observables to produce shirts. We will write that code here later.
...

// 4. The two persons(observables) are doing their job, picking color and logo
color$.next('white');
logo$.next('fish');

color$.next('green');
logo$.next('dog');

color$.next('red');
logo$.next('bird');

color$.next('blue');

// 5. When the two persons (observables) have no more info, they say bye. We will write code here later.
...
```

We created two observables by using [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject). For part 4 in the code, every `.next(<value>)` means Ms. Color or Mr. Logo is picking color or logo.

Take note of the sequence of information (part 4 in our code), here is the summary:

![sequence of info](https://scotch-res.cloudinary.com/image/upload/q_auto:good,f_auto/media/272/6C1OMCYRQwm2puCgTyXf_sequence.gif)

```
1. Ms. Color picks WHITE
2. Mr. Logo picks FISH
3. Ms. Color picks GREEN
4. Mr. Logo picks DOG
5. Ms. Color picks RED
6. Mr. Logo picks BIRD
7. Ms. Color picks BLUE
```

Later, we will update our code (part 3 & 5) to subscribe to both color and logo observables using the four different operators to see how the shirts are produced differently.

All set. Let’s start exploring our first operator!

## zip - the love birds operator

I call the `zip` operator the love birds operator. Love birds need to always be together.

Let’s replace our code (part 3) with below:

```typescript
// 3. We are ready to start printing shirt...
zip(color$, logo$)
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
```

### TL;DR

For those of you who are not familiar with JavaScript ES6/ES2015 [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment), you might find the syntax in subscribe `[color, logo]` a little bit odd.

When we zip `color$` and `logo$`, we expect to receive an array of 2 items during `subscribe`, the first item is `color` and the second is `logo` (follow their orders in `zip` function).

The traditional way of writing it would be `.subscribe((data) => console.log(`${data[0]} shirt with ${data[1]}`))`. As you can see, it’s not very obvious that `data[0]` is color.

ES6 allows us to unpack the value from arrays. Therefore, we unpack `data` into `[color, logo]` straight away.

### Result

Alright, let’s go back to our code and run it. The shirt printing result would be:

![zip - printed shirts](https://scotch-res.cloudinary.com/image/upload/q_auto:good,f_auto/media/272/SYFLX4RmeFoUwjU2lxdQ_zip.gif)

Here is what gets logged to the console:

```
1. white shirt with fish
2. green shirt with dog
3. red shirt with bird
```

### How does `zip` work?

Again, `zip` operator is the love birds operator. In our case, `color` will wait for `logo` whenever there are new values. Both values must change, then only the log gets triggered.

```
1. Ms. Color picks WHITE
2. Mr. Logo picks FISH <- log 01, WHITE + FISH in pair, love birds!
3. Ms. Color picks GREEN
4. Mr. Logo picks DOG <- log 02, GREEN + DOG in pair, love birds!
5. Ms. Color picks RED
6. Mr. Logo picks BIRD <- log 03, RED + BIRD in pair love birds!
7. Ms. Color picks BLUE <- waiting for love...
```

`zip` operator can accept more than 2 observables - no matter how many observables, they must all wait for each other, no man left behind!

## combineLatest

I call `combineLatest` operator the independent operator. They are independent and don’t wait for each other.

Let’s replace the setup code part 3 with the below code:

```typescript
// 3. We are ready to start printing shirt...
combineLatest(color$, logo$)
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
```

The shirt printing result would be:

![combinedLatest - printed shirts](https://scotch-res.cloudinary.com/image/upload/q_auto:good,f_auto/media/272/e5wyQseJR9CoQaOi0Kfw_combinelatest.gif)

Here is what get to log in the console:

```
1. white shirt with fish
2. green shirt with fish
3. green shirt with dog
4. red shirt with dog
5. red shirt with bird
6. blue shirt with bird
```

### How does `combineLatest` work?

In our case, the first function is triggered after both `color` and `logo` values change. From there, either the `color` or `logo` value change will trigger the log.

```
1. Ms. Color picks WHITE
2. Mr. Logo picks FISH <- log 01, color + logo first meet, let's go dutch!
3. Ms. Color picks GREEN <- log 02, GREEN + FISH
4. Mr. Logo picks DOG <- log 03, DOG + GREEN
5. Ms. Color picks RED <- log 04, RED + DOG
6. Mr. Logo picks BIRD <- log 05 BIRD + RED
7. Ms. Color picks BLUE <- log 06 BLUE + BIRD
```

## withLatestFrom

I call `withLatestFrom` operator the primary/secondary operator. At first, the primary must meet the secondary. After that, the primary will take the lead, giving command. The secondary will follow.

Let’s replace the code in part 3 with the below code:

```typescript
// 3. We are ready to start printing shirt...
color$.pipe(withLatestFrom(logo$))
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
```

The shirt printing result would be:

![withLatestFrom - printed shirts](https://scotch-res.cloudinary.com/image/upload/q_auto:good,f_auto/media/272/MKm4pKfdSeoW5YjaTorj_withlatestfrom.gif)

Here is what is logged to the console:

```
1. green shirt with fish
2. red shirt with dog
3. blue shirt with bird
```

### How does `withLatestFrom` work?

Can you guess who is the primary and who is the secondary in our case?

You guessed it! `color` is the primary while `logo` is the secondary. At first (only once), `color`(primary) will look for `logo`(secondary). Once the `logo`(secondary) has responded, `color`(primary) will take the lead. The log will get triggered whenever the next `color`(primary) value is changed. The `logo`(secondary) value changes will not trigger the console log.

```
1. Ms. Color picks WHITE <- nothing happen, waiting for secondary
2. Mr. Logo picks FISH <- secondary found, wait for the primary's command
3. Ms. Color picks GREEN <- log 01, primary says GREEN! So, GREEN + FISH
4. Mr. Logo picks DOG
5. Ms. Color picks RED <- log 02, primary says RED! So, RED + DOG
6. Mr. Logo picks BIRD
7. Ms. Color picks BLUE <- log 03 primary says BLUE! So, BLUE + BIRD
```

## forkJoin

I call `forkJoin` operator the final destination operator because they only commit once all parties are completely true.

Let’s replace the code in part 3 with the following code:

```typescript
// 3. We are ready to start printing shirt...
forkJoin(color$, logo$)
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
```

The shirt printing result would be:
![forkJoin - printed shirts](https://scotch-res.cloudinary.com/image/upload/q_auto:good,f_auto/media/272/r5PfUUWCQmubdxXlWa9C_fork-join-nothing.png)

You will notice that nothing is logged in the console.

In our code, both `color` and `logo` observables are not complete. We can keep pushing value by calling `.next` - that means they are not serious enough and thus they are not `final destination` of each other.

To be serious, we need to `complete` both observables. Replace the code in part 5 with the following:

```typescript
// 5. When the two persons(observables) ...
color$.complete();
logo$.complete();
```

With the above code changes, Here is our shirt printing result:

![forkJoin (complete) - printed shirts](https://scotch-res.cloudinary.com/image/upload/q_auto:good,f_auto/media/272/OQzlcACSVekxWgkuq30A_forkjoin.gif)

This is what is logged to the console:

```
1. blue shirt with bird
```

Here is the sequence of the log:

```
1. Ms. Color picks WHITE
2. Mr. Logo picks FISH
3. Ms. Color picks GREEN
4. Mr. Logo picks DOG
5. Ms. Color picks RED
6. Mr. Logo picks BIRD
7. Ms. Color picks BLUE
8. Ms. Color completed <-- color is serious!
9. Mr. Logo completed <--- log no 01, both logo & color are completed. Final destination!
```

There is more than one way to complete observables. There are operators that allow you to auto-complete observables when conditions are met, for example [take](https://rxjs-dev.firebaseapp.com/api/operators/take), [takeUntil](https://rxjs-dev.firebaseapp.com/api/operators/takeUntil), and [first](https://rxjs-dev.firebaseapp.com/api/operators/first).

Let’s say you only want to make one shirt; you only need to know the first `color` and `logo`. In this case, you don’t care about the rest of the info that Ms. Color & Mr. Logo provide. You can make use of the `take` or `first` operator to achieve auto-complete observables once the first `color` and `logo` emit.

Replace part 3 of the code with the following:

```typescript
// 3. We are ready to start printing shirt...
const firstColor$ = color$.pipe(take(1));
const firstLogo$ = logo$.pipe(first());

forkJoin(firstColor$, firstLogo$)
    .subscribe(([color, logo]) => console.log(`${color} shirt with ${logo}`));
```

 You can remove all the code in part 5 as well. We don’t need the two lines `.complete()` (as previous code) because `take` and `first` will auto-complete the observable when the is condition met.

With the above change, you should see a **white shirt with fish**:

![forkjoin (auto complete) - printed shirtst](https://res.cloudinary.com/scotch/image/upload/v1536601852/1ZfLavc7RnueTUAAvzFS_white_1_eesrye.png)

## Conclusion

Here is the summary of all results:
![one page answer](https://res.cloudinary.com/scotch/image/upload/v1536601871/TZ78oYErQpOU3bFG9krs_Untitled_20design_20_1_20_1_m1fwev.png)

In summary, these four operators trigger the next action (subscribe function in our case) in slightly different conditions.

In some cases, the outcome of using different operators might be the same (that’s why people get confused on which one to use), it would be good to understand the intention of the operator & decide accordingly.

One of the most common use cases of combination operators would be **calling a few APIs, wait for all results return, then executing next logic**. Either `forkJoin` or `zip` will work and return the same result because API calls are one-time only, auto-completed once result is returned (e.g. Angular `httpClient.get`).

However, by understanding the operators more, `forkJoin` might be more suitable in this case. It is because we “seriously” want to wait for all HTTP responses to complete before proceeding to the next step. `zip` is intended for observables with multiple emits. In our case, we expect only one emit for each HTTP request. Therefore, I think `forkJoin` is more appropriate.