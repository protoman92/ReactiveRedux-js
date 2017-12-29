# ReactiveRedux-JS
Rx-based Redux implementation, inspired by https://github.com/Holmusk/HMReactiveRedux-iOS.git.

The first implementation of the store is the RxStore, which can be accessed with:

```typescript
ReduxStore.Rx.create(...reducers: Observable<RxReducer<any>>[]);
```

In order to use this store, we define **BehaviorSubject** instances as action creators, as follows:

```typescript
let action1 = BehaviorSubject<number>(0);
let action2 = BehaviorSubject<string>('');
let action3 = BehaviorSubject<boolean>(false);
```

Then **map** these **BehaviorSubject** objects to emit **RxReducer** whose type signature is as follows:

```typescript
type RxReducer<T> = (state: State.Self<T>): State.Self<T>;
```

Which is a function to be called when new value arrives for a stream. A sample setup is as follows:

```typescript
let pureReducer1: (state: State.Self<any>, value: any) => State.Self<any> = v => {
  return v.updatingValue('a.b.c', value);
};

let reducer1: Observable<RxReducer<any>> = action1.map(v => {
  return (state: State.Self<any>) => pureReducer(state, v);
});

let reducer2 = ...;
let reducer3 = ...;

let store = new ReduxStore.Rx.Self(reducer1, reducer2, reducer3);
let wrapper = store.toWrapper();

wrapper.numberAtNode('a.b.c').doOnNext(console.log).subscribe();

action1.next(1);
action2.next(2);
action3.next(3);
```

Every time the **Subject** pushes a value, it will be pushed onto the state stream and used to compute the next state.