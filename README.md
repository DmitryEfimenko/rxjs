# Neat RxJS utilities

## Installation
```bash
npm install @ngneat/rxjs
```

## Decorators

All decorators are available under `@ngneat/rxjs/decorators`.

### @Share()

This decorator comes handy when you expect a function that returns a completable observable to share that observable for a unique set of parameters of the function.

In the example below only 2 http network requests will be made.

```ts
import { Share } from '@ngneat/rxjs/decorators';

class MyApi {
  constructor(private http: HttpClient) {}

  @Share()
  makeCall(param1: number, param2: number) {
    return this.http.get(`/api/${param1}/${param2}`);
  }
}

class BusinessLogic {
  constructor(private api: MyApi) {
    api.makeCall(1, 2).subscribe();
    api.makeCall(1, 2).subscribe();
    api.makeCall(1, 3).subscribe();
  }
}
```

There might be a use-case when you'd want to disable the functionality of `@Share` decorator based on the function arguments.
To achieve this you can provide the `when` option to specify the condition when the observable should be shared:

In the example below the observable will only be shared when the `param1` is not equal to `param2`.

```ts
@Share({ when: (param1, param2) => param1 !== param2 })
makeCall(param1: number, param2: number) {
  return this.http.get(`/api/${param1}/${param2}`);
}
```
The context of the `when` callback function is the context of the class instance. The signature of the of `when` callback has the same signature as the method that the decorator is applied to.

With the implementation above, two HTTP requests will be made in the example below:
```ts
class BusinessLogic {
  constructor(private api: MyApi) {
    api.makeCall(1, 1).subscribe();
    api.makeCall(1, 1).subscribe();
  }
}
```
