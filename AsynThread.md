# Asyn thread

## std::thread
1. import header
```c++
#include <thread>
```
2. Usage
```c++
std::function<void(int32)> StdThreadFunc = [=](int32 Index)
{
  // @TODO your task.
	for (int32 i = 0; i < 4; i++)
	{
		UE_LOG(TestLog, Log, TEXT("StdThread %d Run: %d."), Index, i);
		FPlatformProcess::Sleep(1.f);
	}
};
new std::thread(StdThreadFunc, 0);
```
## FRunnable
1. Implement FRunnable
```c++
class FTestRunnableThread :public FRunnable
{
private:
	int32 Index;
public:
	FTestRunnableThread(int32 _Index):Index(_Index){}

	virtual bool Init()override
	{
		UE_LOG(TestLog, Log, TEXT("TestRunnableThread %d Init."), Index);
		return true;
	}

	virtual uint32 Run()override
	{
    // @TODO your task.
		for (int32 i = 0; i < 4; i++)
		{
			UE_LOG(TestLog, Log, TEXT("TestRunnableThread %d Run: %d."), Index, i);
			FPlatformProcess::Sleep(1.f);
		}
		return 0;
	}

	virtual void Exit()override
	{
		UE_LOG(TestLog, Log, TEXT("TestRunnableThread %d Exit."), Index);
	}
};
```
2. Usage
```c++
FRunnableThread::Create(new FTestRunnableThread(0), TEXT("TestRunnableThread"));
```
## AsynTask
1. 
```c++
class ExampleAsyncTask : public FNonAbandonableTask
{
	friend class FAsyncTask<ExampleAsyncTask>;

	int32 ExampleData;

	ExampleAsyncTask(int32 InExampleData)
		: ExampleData(InExampleData)
	{
	}

	void DoWork()
	{
    // @TODO your task.
		for (int32 i = 0; i < 4; i++)
		{
			UE_LOG(TestLog, Log, TEXT("ExampleAsyncTask %d Run: %d."), ExampleData, i);
			FPlatformProcess::Sleep(1.f);
		}
	}

	FORCEINLINE TStatId GetStatId() const
	{
		RETURN_QUICK_DECLARE_CYCLE_STAT(ExampleAsyncTask, STATGROUP_ThreadPoolAsyncTasks);
	}
};
```
2. start an example job
```c++
FAsyncTask<ExampleAsyncTask>* MyTask = new FAsyncTask<ExampleAsyncTask>( 5 );
MyTask->StartBackgroundTask();
//--or --
MyTask->StartSynchronousTask();
```
3. to just do it now on this thread. Check if the task is done :
```c++
if (MyTask->IsDone())
{
}
```
Spinning on IsDone is not acceptable( see EnsureCompletion ), but it is ok to check once a frame.
Ensure the task is done, doing the task on the current thread if it has not been started, waiting until completion in all cases.
```c++
MyTask->EnsureCompletion();
```
4. 
```c++
delete Task;
```
## TaskGraph
1. override the necessary function via custom class.
```c++
class FTestGraphTask
{
private:
	int32 Index;

public:
	FTestGraphTask(int32 _Index) :Index(_Index) {}

	static const TCHAR* GetTaskName()
	{
		return TEXT("TestTask");
	}

	FORCEINLINE static TStatId GetStatId()
	{
		RETURN_QUICK_DECLARE_CYCLE_STAT(FTestGraphTask, STATGROUP_TaskGraphTasks);
	}

	static ENamedThreads::Type GetDesiredThread()
	{
		return ENamedThreads::AnyThread;
	}

	static ESubsequentsMode::Type GetSubsequentsMode()
	{
		return ESubsequentsMode::TrackSubsequents;
	}

	void DoTask(ENamedThreads::Type CurrentThread, const FGraphEventRef& MyCompletionGraphEvent)
	{
		for (int32 i = 0; i < 5; i++)
		{
      // @TODO your task.
			UE_LOG(TestLog, Log, TEXT("TestGraphTask %d Run: %d"), Index, i);
			FPlatformProcess::Sleep(1.f);
		}
	}
};
```
2. Usage
```c++
TGraphTask<FTestGraphTask>::CreateTask(NULL, ENamedThreads::GameThread).ConstructAndDispatchWhenReady(0);
```

## 异步蓝图节点(只能用一种委托)

`BlueprintAsyncActionBase.h`

`AsyncWork.h`