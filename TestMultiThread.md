# Synchronizing Multi thread in testing

Concurrency is important in modern software development. On the other hand, multi-threaded programming is difficult and it complicates automatic testing. Concurrency and synchronization are system wide concerns. Functionality and concurrency are different concerns for a system.

Unit tests shall be done for a behavor within a single thread. However it is unavoidable for testing bigger granularity component, e.g. BDD, to deal with multi-threads. The main concern of unit test and component test is their functionalities. We can automatically test functionalities of muti-threaded program if test programs synchronize multi-threads properly. Tests must synchronize with the background threads, so that they don’t make assertions before the threads have finished working or leave threads running that might interfere with later tests.

Here we introduce a simple and effective C++ implementation of event-based syncronization method, the idea is from [Growing Object-Oriented Software, Guided by Tests](http://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627). An assertion of a test waits for an event by blocking on a notification trace monitor until it gets notified or times out. When the monitor is notified, the test thread wakes up and continues if it finds that the expected event has arrived, or blocks again. If the test times out, then it raises a time our exception. Processing sequence is as following.  
![syncronize test and SUT](https://github.com/bzquan/Documents/blob/master/Images/notification_trace.jpg)
  
__AbstractNotificationTrace.h__

``` c++
  
    #pragma once

    #include <string>
    #include <chrono>

    class AbstractNotificationTrace
    {
        public:
	    virtual ~AbstractNotificationTrace(){}
	    virtual void Append(std::string notification_msg){}
	    virtual void WaitNotification(std::string expected_msg, std::chrono::milliseconds wait_time){}
    };
 ```

Production code uses AbstractNotificationTrace, which does nothing in production mode. An Object of  AbstractNotificationTrace shall be system wide, i.e. it is a singleton. Singleton design pattern is an antipattern for TDD. Therefore it needs a proper way to inject dependancy of this object to the system. Test programs use NotificationTrace which is an implementation of AbstractNotificationTrace.

__NotificationTrace.h__

``` c++
  
    #pragma once

    #include <vector>
    #include <mutex>
    #include <condition_variable>
    #include "AbstractNotificationTrace.h"

    class NotificationTrace : public AbstractNotificationTrace
    {
    public:
        NotificationTrace() = default;

        void Append(std::string notification_msg) override;
        void WaitNotification(std::string expected_msg, std::chrono::milliseconds wait_time) override;

    private:
        bool Match(std::string expected_msg);

    private:
	    std::vector<std::string> m_Trace;
	    std::mutex m_Mutex;
	    std::condition_variable m_Condition;
    };
 ```

Any event would wake up test program. However test program should wait a specific event otherwise it should enter waiting state again. It is implemented by using wait\_until function of std::condition_variable.  
__NotificationTrace.cpp__

``` c++

    #include <algorithm>
    #include <iostream>
    #include "NotificationTrace.h"

    using namespace std;

    void NotificationTrace::Append(std::string notification_msg)
    {
        std::lock_guard<std::mutex> lock(m_Mutex);
        m_Trace.push_back(notification_msg);
        m_Condition.notify_all();
    }

    void NotificationTrace::WaitNotification(std::string expected_msg, std::chrono::milliseconds wait_time)
    {
        auto wait_until = std::chrono::system_clock::now() + wait_time;
        unique_lock<std::mutex> lock(m_Mutex);

        if (!m_Condition.wait_until(lock, wait_until, [this, expected_msg] { return Match(expected_msg); }))
        {
            throw domain_error("Time out in WaitNotification!");
        }
    }

    bool NotificationTrace::Match(std::string expected_msg)
    {
        return find(m_Trace.begin(), m_Trace.end(), expected_msg) != m_Trace.end();
    }  
 ```

A test program requests SUT to do an asynchronous work and wait notification trace event, after then makes an assertion. Test case can assert synchronously expected result if "a notification msg" event has been arrived within 1000ms, otherwise the test case will fail by time out exception.

``` c++

    TEST_F(MyBackgourndServiceTest, AsynchronousWork)
    {
        // Given
        Some preconditions

		// When
        Request SUT do an asynchronous work

		// Then
        shared_ptr<AbstractNotificationTrace> notification_trace = get NotificationTrace object some how;
        notification_trace->WaitNotification("a notification msg", 1000ms);

        ASSERT_EQ(expected, SUT.GetResult());
```