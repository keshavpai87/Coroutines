# Coroutines
Coroutines is a sequence of well managed subtasks.
To an extent we can consider it as a light weight thread.
We can execute many coroutines in a single thread which means it can be suspended in one thread and resumed from another thread
Coroutines API allows us to write asynchronous codes in a sequential manner, hence it avoids unnecessary boiler plate codes and makes our code more readable and maintainable.

**Importance of Asynchronous Programming**

Create a new project and add 2 textviews and 2 button

![image](https://github.com/user-attachments/assets/0323051d-8377-4d47-8947-fca70ac263c6)

![image](https://github.com/user-attachments/assets/798d60d1-dd38-4627-b1d5-c8189189d6d8)

If we click on the DownloadUserData buttonn and then simultaneously if we click on the ClickHere button 5 times, we can see that it gets freezed for a while and at a sudden we can see the 
count value changed to 5. The reason behind it is the heavy running task on the main thread

To overcome this issue, we use Couroutines. 
One thread can have multiple coroutines executing in it at the same time. Coroutines are just like separate processes running on a thread. 
Also if a coroutine is suspended in one thread, it can be resumed in other thread

We need to add the coroutine dependencies added at the app level from the folllowing link

https://developer.android.com/kotlin/coroutines


