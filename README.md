# Coroutines
Coroutines is a sequence of well managed subtasks.
To an extent we can consider it as a light weight thread.
We can execute many coroutines in a single thread which means it can be suspended in one thread and resumed from another thread
Coroutines API allows us to write asynchronous codes in a sequential manner, hence it avoids unnecessary boiler plate codes and makes our code more readable and maintainable.

**Importance of Asynchronous Programming**

Create a new project and add 2 textviews and 2 button as below

![image](https://github.com/user-attachments/assets/0323051d-8377-4d47-8947-fca70ac263c6)

![image](https://github.com/user-attachments/assets/798d60d1-dd38-4627-b1d5-c8189189d6d8)

If we click on the DownloadUserData buttonn and then simultaneously if we click on the ClickHere button 5 times, we can see that it gets freezed for a while and at a sudden we can see the 
count value changed to 5. The reason behind it is the heavy running task on the main thread

To overcome this issue, we use Couroutines. 
One thread can have multiple coroutines executing in it at the same time. Coroutines are just like separate processes running on a thread. 
Also if a coroutine is suspended in one thread, it can be resumed in other thread

We need to add the coroutine dependencies added at the app level from the folllowing link

https://developer.android.com/kotlin/coroutines

Now adding a coroutine to the existing code. The changes required are as below

![image](https://github.com/user-attachments/assets/c00cd8bd-ed8c-4ebf-9d4c-25eba895359f)

**Scopes, Dispatchers and Builders**

In kotlin coroutines, we must start all the coroutines within the scope. Using the properties of the scope, we can easily keep track of coroutines, cancel the coroutine and handle errors or exceptions thrown by coroutines. 
**CoroutineScope** is an interface that provides the scope of the coroutine

**GlobalScope** is used to launch top level coroutine which are operating on the application lifetime. We hardly use it

**Dispatcher.IO** acts as a reference to the coroutine context. 
We need to provide the context and all the coroutines that are going to start within this scope will be running on this context. We need to run this in the background thread. So we need to provide **Dispatchers.IO**. 
Dispatcher describes the kind of thread where the coroutine should be run. In Kotlin Android structured concurrency, it is always recommended to start coroutines using main thread and then switch to background threads.  

To launch coroutines in the main thread, we use Dispatcher.Main.

We also have IO dispatcher.

Dispatchers.IO,

Dispatchers.Default,

Dispatchers.Unconfined.

So, if we use Dispatcher.Main, the coroutine will run in the Main Thread or UI thread.

As there is only one Main or UI thread, if we create 10 coroutines within a scope with Dispatchers.Main as the context, all those 10 coroutines will run in the same Main thread.
We only use main dispatcher for small, light weight tasks like call to a UI function, call to a suspending function or to get updates from the livedata. 
In structured concurrency, the best practice is always starting a coroutine from the Main thread and later switching it to a background thread.

If we use **Dispatchers.IO**, the coroutine will run in a background thread from a shared pool of on-demand created threads. We use this IO dispatcher to work with local database, communicate with network and to work with files.

**Dispatcher.Default** is used for CPU intensive tasks such as sorting a list of data with 10000 list items or parsing a huge JSON file with details of 100000 movies.

**Dispatcher.Unconfined** is a dispatcher used with GlobalScope. If we use Dispatchers.Unconfined, Coroutine will run on the current thread but if it is suspended and resumed, it will run on whichever thread that the suspending function is running on. It is not recommended to use this dispatcher for Android Development. More often, we use Dispatchers.Main and Dispatchers.IO

![image](https://github.com/user-attachments/assets/785accc5-11b8-4b8a-857d-211791ebe155)

**Launch** is the CoroutineBuilder. Coroutine builders are extension functions of coroutine scopes, which are used to launch a new coroutine. 

Types of Coroutine Builders:
1. Launch
2. Async
3. RunBlocking
4. Produce

![image](https://github.com/user-attachments/assets/24d9bb67-462d-47a2-9f31-c58e6b9a1fce)

1. **Launch** coroutine builder launches a new coroutine without blocking the current thread. This builder **returns** an instance of **job** which can be used as a reference to the co routine. We can use that returned job instance to keep track of the coroutine and to cancel the coroutine. We use **launch** builder for coroutines that does not have **any result** as the **return value**. This builder returns a Job instance but does not return any computational result. We cannot use this coroutine to calculate something and get the final answer as the returned value. If we want get such result as a returned value we should use **Async** coroutine builder.

2. **Async** coroutine builder allows us to launch multiple coroutines in parallel. Async builder also launches a new coroutine without blocking the current thread. This builder **returns** an instance of **Deferred<T>** type of the result. We use this builder for coroutines that does have a result as the return value. Actually deferred interface is an **extension** of **job interface**. So we can use it like we use job for things like cancelling a coroutine.
If our result is a string value, the type would be string, If our result is an Int value, the type would be Int. So, to get the value from the deferred object, we need to invoke its **await()** function.

In android development, **Launch** and **Async** builders are the coroutine builders we use most of the time.

3. **Produce** builder is for coroutines which produces a stream of elements. This builder **returns** an instance of **ReceiveChannel**. 

4. **Runblocking** builder is mostly **used** for **testing**. It blocks the thread while the coroutine is executing. It **returns** a result of type **T**

**Structured Concurrency** is a set of language features and best practices introduced for Kotlin Coroutines to avoid coroutines leaks and manage the coroutines productively.

**Switch the thread of a coroutine**

As its observed that onClick of DownloadUserData, we are displaying the results in the logs. Now we shall make the changes to display it on the UI ie on tvUserMessage textview. As we are using Dispatcher.IO, now the long running task is executing in the background thread.

![image](https://github.com/user-attachments/assets/772be579-b03d-45d8-a9d3-56f1ecd44c53)

In Android, we directly cannot call to view component running in ui thread from background thread like the above. If we run now, it will crash showing a CalledFromWrongThreadException. Only the original thread that created a view hierarchy can touch its views. Therefore, we have to call views from the UI thread. But fortunately, coroutines has the easiest way to switch
between threads. Using **withContext()** function, which is a **suspending** function, we can switch a coroutine from one thread to another. Suspending Function cannot be called from a normal function.

![image](https://github.com/user-attachments/assets/a49021e8-1e6b-4ed8-881c-55c362149a32)

![image](https://github.com/user-attachments/assets/95ee8961-84ed-4653-bd46-913842c67fe1)

**Suspending Functions**

In kotlin coroutines, whenever a coroutine is suspended, the current stack frame of the function is copied and saved in the memory. When the function resumes, after completing its task,
the stack frame is copied back from where it was saved and starts running again. Kotlin coroutines API provides us a lot of functions to make our works easier. Almost all of them are suspending functions.

withContext(), withTimeout(), withTimeoutOrNull(), join(), delay(), await(), supervisorScope, coroutineScope. Other than this, there are many other suspending functions. These are some examples of suspending functions provided by the Kotlin coroutines API. Not only coroutines library, other libraries such as room and retrofit also provides suspending funcitons to support us to work with coroutines. If we are ever going to call those first class suspending functions from our functions, we have to mark our functions with suspend modifier.

If we are going to use a suspending function such as withContext(), we have to mark our calling function with suspend modifier. And also if we are going to invoke another suspending function created by us, we have to also mark that calling function with suspend modifier. With suspending modifier, we are actually limiting the use of the function only for coroutines. A suspending function can be called from a coroutine block or from another suspending function only. We cannot invoke a suspending function from a normal function or from other places of the code.

And remember, a coroutine can invoke both suspending and regular functions, but a suspending function can be invoked by a coroutine only. We use suspending functions to avoid thread blockings and hence to provide smooth, uninterrupted experience to the user.

**Async and Await**

Let’s assume we have to get the result from four online data sources and combine them all to show the final result to the user.. Suppose T1 => 10s, T2 => 15s, T3 => 12s, T4 => 13s. Altogether it takes 50s to complete and get the result which is a long wait and a bad experience to the user. To complete thia at the earliest, we need to run all the taks in parallel and we can complete them in 15s. Writing the codes to complete the taska dn combining them all together is called parallel decomposition and this can be achieved using **Async and Await** functions.
