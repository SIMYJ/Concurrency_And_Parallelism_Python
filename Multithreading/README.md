# Multithreading

# 1.Python's GIL

```
- 파이썬에만 있는 독특한 특징 Gil(Global Interpreter Lock)
- Gil를 알고 있으면 파이썬의 멀티쓰레딩 프로그램 만들때 이해하기 유용하다.
 ```


## Gil(Global Interpreter Lock)
- 파이썬에서 쓰레드을 여러 개 생성한다고 해서 여러 개의 쓰레드가 동시에 실행되는 것은 아니다. 정확히 말하자면 두 개의 쓰레드가 동시에 실행되는 것처럼 보일 뿐, 특정 시점에서는 여러 개의 쓰레드 중 하나의 쓰레드만 실행된다.

<img src="https://i.imgur.com/1iKHUcJ.png" width="500px">

```
위 그림과 같이 thread를 동시에 실행하려고 해도 한 타임에 한 thread만 실행된다.
멀티스레드로 실행할때보다 하나의 스레드로 실행하는것이 성능이 더 낫다고해서 gill을 구현하였다고 한다.
```



## Gil 특징
-  (1). py파일을 Cpython이 내부적으로 해석을 한다.(python파일을 bytecode로 변경)
- CPython -> Python(bytecode) 실행 시 여러 thread 사용 할 경우
	- 단일 스레드만이 Python object에 접근 하게 제한하는 mutex



```
용어정리

  - CPython
      ‣ 대부분의 파이썬의 경우 내부는 C언어로 구현되어 있다. 이를 CPython이라고 한다.
  - Mutex(binary semaphore)
      ‣ 임계구역에 하나의 스레드만 들어갈 수 있음
  - Semaphore
      ‣ 임계구역에 여러 스레드가 들어갈 수 있음
```

-  (2).CPython 메모리 관리가 취약하기 때문(즉, Thread-safe)

-  (3).단일 스레드도 충분히 빠르다.
-  (4).프로세스 사용 가능(Numpy/Scipy)등 Gil 외부 영역에서 효율적인 가능 코딩
-  (5).병렬 처리는 Multiprocessing, asyncio 선택지 다양함.
-  <del>(Python).6 thread 동시성 완벽 처리를 -> 위해서, Jython, IronPython Stackless 등이 존재</del>








https://jeleedev.tistory.com/24    
https://m.blog.naver.com/alice_k106/221566619995


---




# 2.Thread(1) - Basic
- Keyword - threading basic
- Main Thread와 Sub(Child) Thread

-  ***logging, threading, time는 파이썬 빌드인패키지이기때문에 파이썬에 내장되어 있다.***    
-  ***스레드는 디버깅이 어렵기 때문에 logging패키징을 통해서 로그를 확인한다.(디버깅역할)***     
-  ***if __name__ == "__main__" : => 메인영역이 실행되면 Main스레드가 자동으로 생성된다.***     
-  ***.start()는 스레드 시작***     
-  ***.join()는 서브스레드가 끝날때 까지 메인스레드가 대기한다.***      


## x.join()를 사용하지 않은 코드

```python
# logging threading,time 파이썬 빌드인패키지이여서 파이썬에 내장되어 있다.
import logging
import threading
import time

# 스레드 실행 함수
def thread_func(name):

    logging.info("Sub-Thread %s: starting", name)
    time.sleep(3)
    logging.info("Sub-Thread %s: finishing", name)

# 메인 영역
if __name__ == "__main__":
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")
    logging.info("Main-Thread : before creating thread")
    #현재까지 스레드 한개
    ## 스레드는 디버그깅이 어렵기 때문에 logging패키징을 통해서 로그를 확인한다.(디버깅역할)
   

    # 함수 인자 확인
    x = threading.Thread(target=thread_func, args=('First',))
    
    logging.info("Main-Thread : before running thread")
   
    # 서브 스레드 시작
    x.start()
   
    logging.info("Main-Thread : wait for the thread to finish")
    
    # 주석 전후 결과 확인
    # x.join()
    
    logging.info("Main-Thread : all done")

```
```
03:37:44: Main-Thread : before creating thread
03:37:44: Main-Thread : before running thread
03:37:44: Sub-Thread First: starting
03:37:44: Main-Thread : wait for the thread to finish
03:37:44: Main-Thread : all done
03:37:47: Sub-Thread First: finishing

```





## x.join()를 사용한 코드

```python

# 이 밑의 3개는 파이썬 빌드인 패키지/내장되어있다.
import logging
import threding
import time 

# 프로세스에서는 하나의 메인 스레드가 반드 존재한다!

# 메인 영역
if __name__ == "__main__" :
    # Logging format 설저어
    format = "%(asctime)s: %(mesaage)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt=%H:%M:%S)
    logging.info("Main-Thread: before creating thread")
    #현재까지 스레드 한개
    ## 스레드는 디버그깅이 어렵기 때문에 logging패키징을 통해서 로그를 확인한다.(디버깅역할)


    #함수 인자 확인
    x = threading.Thread(target=thread_func, args==(''))

    logging.info("Main-Thread: before running thread")

    #서브 스레드 시작
    x.start()

    logging.info("Main-Thread : wait for the thread to finish")
    
    # 주석 전후 결과 확인
     x.join()
    
    logging.info("Main-Thread : all done")


#스레드 실행 함수
def thread_func(name):
    logging.info("Sub-Thread %s: starting", name)
    time.sleep(3)
    logging.info("Sub-Thread %s: finishing", name)
```


```
02:04:26: Main-Thread : before creating thread
02:04:26: Main-Thread : before running thread
02:04:26: Sub-Thread First: starting
02:04:26: Main-Thread : wait for the thread to finish
02:04:29: Sub-Thread First: finishing
02:04:29: Main-Thread : all done

```

---

# Thread(2) - Daemon, Join
- Keyword - DeamonThread, Join

## DaemonThread(데몬스레드)

-  ***(1). 백그라운드에서 실행***      
-  ***(2). 메인 스레드 종료시 즉시 종료()***          
        - [2.Thread(1) - Basic]에서의 메인스레드 종료후 서브스레드가 종료되었다.     
-  ***(3). 일반 스레드는 작업 종료시 까지 실행***     
-  ***(4). 주로 백그라운드 무한 대기 시 이벤트 발생시 실행하는 부분 담당***     
        - JVM(가비지 컬렉션), 워드,한글(자동저장),웹서버에서도 사용
```
  JVM(가비지 컬렉션)
   - 끝까지 남아서 사용하지 않는 메모리의 오브젝트의 주소 공간을 클리어해줌(메모리관리 효율적으로 해줌)
 
  문서 작성시 자동저장
   - 메인스레드 => 문서 작성 영역
   - 데몬스레드 => 문서 자동저장, 문서 종료시(저장이벤트출력) => 즉 메인스레드 종료시 데몬스레드 종료
```


## 코드 팁
### ***x.isDaemon()*** 
- x스레드가 데몬스레드이면 true, 데몬스레드가 아니면 false 리턴

### ***threading.Thread(,,) 마지막 인자 Default= false이다.(데몬스레드 사용안함)***
```
 x = threading.Thread(target=thread_func, args=('Second', range(10000)), daemon=True)
 x = threading.Thread(target=스레드동작함수, args=(스레드명, 전달인자), daemon=True)
	
```


## 일반 스레드 x,y 돌릴때(코드)
```python
import logging
import threading
import time


# 스레드 실행 함수
def thread_func(name, d):
    logging.info("Sub-Thread %s: starting", name)

    for i in d:
        #pass
         print(i)

    logging.info("Sub-Thread %s: finishing", name)

# 메인 영역
if __name__ == "__main__":
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")
    logging.info("Main-Thread : before creating thread")
    
    # 함수 인자 확인
    # Deamon : Default False
    x = threading.Thread(target=thread_func, args=('First', range(2)))
    # x.daemon = True

    y = threading.Thread(target=thread_func, args=('Second', range(5)))
    
    logging.info("Main-Thread : before running thread")
    
    # 서브 스레드 시작
    x.start()
    y.start()

    # DaemonThread 확인
    # print(x.isDaemon())
   
    logging.info("Main-Thread : wait for the thread to finish")
    
    # 주석 전후 결과 확인
    # x.join() 
    # y.join()
    
    logging.info("Main-Thread : all done")
```
```
15:52:06: Main-Thread : before creating thread
15:52:06: Main-Thread : before running thread
15:52:06: Sub-Thread First: starting
0
1
15:52:06: Sub-Thread First: finishing
15:52:06: Sub-Thread Second: starting
0
15:52:06: Main-Thread : wait for the thread to finish
1
15:52:06: Main-Thread : all done
2
3
4
15:52:06: Sub-Thread Second: finishing

```


## 데몬 스레드 x,y 돌릴때(코드)
```python
import logging
import threading
import time

# 스레드 실행 함수
def thread_func(name, d):
    logging.info("Sub-Thread %s: starting", name)

    for i in d:
        #pass
        print(i)

    logging.info("Sub-Thread %s: finishing", name)

# 메인 영역
if __name__ == "__main__":
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")
    logging.info("Main-Thread : before creating thread")
    
    # 함수 인자 확인
    # Deamon : Default False
    x = threading.Thread(target=thread_func, args=('First', range(2)), daemon=True)
    # x.daemon = True

    y = threading.Thread(target=thread_func, args=('Second', range(5)), daemon=True)
    
    logging.info("Main-Thread : before running thread")
    
    # 서브 스레드 시작
    x.start()
    y.start()

    # DaemonThread 확인
    # print(x.isDaemon())
   
    logging.info("Main-Thread : wait for the thread to finish")
    
    # 주석 전후 결과 확인
    # x.join() 
    # y.join()
    
    logging.info("Main-Thread : all done")
```

```
15:55:31: Main-Thread : before creating thread
15:55:31: Main-Thread : before running thread
15:55:31: Sub-Thread First: starting
0
1
15:55:31: Sub-Thread First: finishing
15:55:31: Sub-Thread Second: starting
0
1
2
3
4
15:55:31: Sub-Thread Second: finishing
15:55:31: Main-Thread : wait for the thread to finish
15:55:31: Main-Thread : all done
```



## 데몬스레드에 대한 생각!!!

- 데몬을 사용했는데 굳이 join()사용하면 끝까지 실행????= > 비효율적

- 일반 스레드가 종료되기전에 라이프사이클안에서 충분히 데몬스레드를 사용한다. 요청있을때마다 데몬스레드가 생성되고 요청 해결후 종료되고, 해당 구조안에서 데몬스레드가 정확하게 비즈니스로직에 흐름에 알맞게 구조적으로 갖춰서 코딩을 해야한다.(데몬스레드를 많이 사용하면 좋다고 한다.)


- 메인스레드와 생명주기를 같이하는 코딩을 하자 메인스레드 안에 데몬스레드를 적절히 사용!!

- 부모 스레드와 child스레드 개념을 생각하자.!!


---

# Thread(3) - ThreadPoolExecutor
﻿


Keyword - Many Threads, concurrent.futures, (xxx)PoolExcutor



## main Thread = group Thread
- ***(1).Python 3.2 이상 표준 라이브러리 사용***
- ***(2).concurrent.futures***
- ***(3).with사용으로 생성, 소멸 라이프사이클 관리 용이***
- ***(4).디버깅하기가 난해함(단점)***
- ***(5).대기중인 작업 -> Queue넣음 -> 완료 상태 조사 -> 결과 또는 예외 받아온다. -> 단일화(캡슐화)***


```
from concurrent.futures import ThreadPoolExecutor - 스레드  사용
from concurrent.futures import ProcessPoolExecutor -> 멀티프로세싱 사용

ThreadPoolExecutor는 스레드를 편하게 사용할수 있도록 만들어진 패키지
사용하지 않으면 스레드관리 등등을 직접 관리하기는 어럽기때문에 이 패키지를 사용한다.

```


- ***Futures***
```
파이썬 3.2버전부터 여러스레드를 사용하기 쉽게 하기위해서 concurrent.futures패키지 제공

```

- ***task(name)함수*** => 스레드가 별도로 실행할 함수

- ***max_workers***
```
예시 설명
with ThreadPoolExecutor(max_workers=1) as executor: 
    future = executor.submit(wait_function, 3, 4)
    future2 = executor.submit(wait_function, 8, 8)


 max_workers은를 먼저 1로 설정하면 그것을 실행하면 작업이 병렬로 실행되지 않는다는 것을 알 수 있습니다. 첫 번째 작업을 실행 한 다음 두 번째 작업을 실행합니다. 이는 주로 풀에 작업자가 한 명뿐이기 때문입니다. max_workers를 2로 늘리면 두 작업이 병렬로 실행되는 것을 볼 수 있습니다.

https://ichi.pro/ko/python-eseo-byeonglyeol-jag-eob-eul-sijaghaneun-bangbeob-243366637556419

```


## 실행 방법1 (쓰레드를 일일이 생성)
```python

import logging
from concurrent.futures import ThreadPoolExecutor
import time

# 스레드가 별도로 실행할 함수 task()
# 스레드 실행 함수
def task(name):
    logging.info("Sub-Thread %s: starting", name)

    result = 0
    for i in range(10001):
        result = result + i

    logging.info("Sub-Thread %s: finishing result: %d", name, result)

    # return 1


# 메인 영역
def main():
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")

    logging.info("Main-Thread : before creating and running thread")

    # 실행 방법1
    # max_workers : 작업의 개수가 남어가면 직접 설정이 유리
    executor = ThreadPoolExecutor(max_workers=3)
    
    task1 = executor.submit(task, ('First',))
    task2 = executor.submit(task, ('Second',))

    # 결과 값 있을 경우
    print(task1.result())
    print(task2.result())

    

    logging.info("Main-Thread : all done")

if __name__ == '__main__':
    main()
  
```

```
15:27:46: Main-Thread : before creating and running thread
15:27:46: Sub-Thread ('First',): starting
15:27:46: Sub-Thread ('First',): finishing result: 50005000
None
15:27:46: Sub-Thread ('Second',): starting
15:27:46: Sub-Thread ('Second',): finishing result: 50005000
None
15:27:46: Main-Thread : all done
```


##  실행 방법2 (쓰레드 생성시 간편)
- with context 구문 사용
```python
import logging
from concurrent.futures import ThreadPoolExecutor
import time

# 스레드가 별도로 실행할 함수 task()
# 스레드 실행 함수
def task(name):
    logging.info("Sub-Thread %s: starting", name)

    result = 0
    for i in range(10001):
        result = result + i

    logging.info("Sub-Thread %s: finishing result: %d", name, result)

    # return 1


# 메인 영역
def main():
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")

    logging.info("Main-Thread : before creating and running thread")


    # 실행 방법2
    # with context 구문 사용

    with ThreadPoolExecutor(max_workers=3) as executor:
        tasks = executor.map(task, ['First', 'Second'])
        
        # 결과 확인
        print(list(tasks))

    logging.info("Main-Thread : all done")

if __name__ == '__main__':
    main()

```

```
16:19:23: Main-Thread : before creating and running thread
16:19:23: Sub-Thread First: starting
16:19:23: Sub-Thread First: finishing result: 50005000
16:19:23: Sub-Thread Second: starting
16:19:23: Sub-Thread Second: finishing result: 50005000
[None, None]
16:19:23: Main-Thread : all done
```
 

---

# Thread(4) - Lock, Deadlock
﻿- Keyword - Lock, Deadlock, Race Condition, Thread synchronization

##용어 설명
- (1).세마포어(Semaphore) : 프로세스간 공유된 자원에 접근 시 문제 발생 가능성 
    - > 한 개의 프로세스만 접근 처리 고안(경쟁상태 예방)
- (2).뮤텍스(Mutex) : 공유된 자원의 데이터를 여러 스레드가 접근하는 것을 막는 것.-> 경쟁상태 예방
- (3).Lock : 상호 배제를 위한 잠금(Lock)처리(데이터 경쟁)
- (4).데드락(Deadlock) : 프로세스가 자원을 획득하지 못해 다음 처리를 못하는 무한 대기 상황(교착상태)
- (5).Thread synchronization(스레드 동기화)를 통해서 안정적으로 동작하게 처리한다.(동기화메소드, 동기화 블록)
- (6).Semaphore와 Mutex의 차이점?    
    - > 세마포어와 뮤텍스 개체는 모두 병렬 프로그래밍 환경에서 상호 배제를 위해 사용      
    - > 뮤텍스 개체는 단일 스레드가 리소스 또는 중요 섹션을 소비 허용     
    - > 세마포는 리소스에 대한 제한된 수의 동시 액세스를 허용    

<img src="https://i.imgur.com/kMSfsqi.jpg" width="300">

```
﻿- 스레드를 사용할때 왜 락과 데드락을 공부해야되는가?
    - > 경쟁 상태를 예방하기 위한 동기화 작업 ( 세마포어, 뮤텍스)


- 세마포어는 뮤텍스가 될수 있지만 뮤텍스는 세마포어가 될 수 없다.


- self.value = 0 ## Data나 힙영역으로 변수를 공유할것이다.


- 스택영역: 스레드별로 함수를 호출할때 함수내 나만의 변수를 선언하여 계산할때 필요 / update함수를 사용하기 위해서는 스택영역이 필요합니다. (local_copy는 스택영역)
```

## 동기화 되지 않는 코드

```python

import logging
from concurrent.futures import ThreadPoolExecutor
import time


class FakeDataStore:
    # 공유 변수(value) 예로 화장실
    # 별도의 스택영역을 가진다.
    # 여러 스레드는 self.vallue를 공유한다.
    def __init__(self):
        self.value = 0 ##  Data나 힙영역으로 변수를 공유할것이다.
        
    # 변수 업데이트 함수
    def update(self, n):
        logging.info("Thread %s: starting update", n)

        # 뮤텍스 & Lock 등 동기화(Thread synchronization) 필요
        local_copy = self.value # local_copy는 스택영역
        local_copy += 1
        time.sleep(0.1)
        self.value = local_copy

        logging.info("Thread %s: finishing update", n)


if __name__ == "__main__":
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")

    # 클래스 인스턴스화
    store = FakeDataStore()

    logging.info("Testing update. Starting value is %d.", store.value)

    # With Context 시작
    with ThreadPoolExecutor(max_workers=2) as executor:
        for n in ['First', 'Second', 'Third']:
            executor.submit(store.update, n)

    logging.info("Testing update. Ending value is %d.", store.value)    
   


        
﻿```
01:52:05: Testing update. Starting value is 0.
01:52:05: Thread First: starting update
01:52:05: Thread Second: starting update
01:52:05: Thread First: finishing update
01:52:05: Thread Second: finishing update
01:52:05: Thread Third: starting update
01:52:05: Thread Third: finishing update
01:52:05: Testing update. Ending value is 2.
```

---


```

[
with ThreadPoolExecutor(max_workers=2) as executor:
    for n in ['First', 'Second', 'Third']:
        executor.submit(store.update, n)
]
코드로 3개의 스레드를 실행한다.
즉 그러면  logging.info("Testing update. Ending value is %d.", store.value) 로깅값으로 3개의 스레드가 작동했으므려 
01:52:05: Testing update. Ending value is 3 출력으로 예상되었지만.
01:52:05: Testing update. Ending value is 2. 가 출력된다.

동기화가 되지 않았기때문에 2로 출력된다. 

1: local_copy = self.value # local_copy는 스택영역
2: local_copy += 1
3: time.sleep(0.1)
4: self.value = local_copy
위 코드에서 (4)self.value = local_copy가  업데이트가 업데이트되지 않은 상태에서  (1)local_copy = self.value 가 실행되었을것이다.

```



## 동기화 된 코드 (방식1)

```python

import logging
from concurrent.futures import ThreadPoolExecutor
import time
import threading


class FakeDataStore:
    # 공유 변수(value)
    # 여러 스레드는 self.vallue를 공유한다.
    def __init__(self):
        self.value = 0
        # Lock 선언
        self._lock = threading.Lock()

    # 변수 업데이트 함수
    def update(self, n):
        logging.info("Thread %s: starting update", n)

        # 뮤텍스 & Lock 등 동기화(Thread synchronization) 필요
        
        # Lock 획득(방법1)
        self._lock.acquire()
        logging.info("Thread %s has lock", n)
        
        local_copy = self.value
        local_copy += 1
        time.sleep(0.1)
        self.value = local_copy

        logging.info("Thread %s about to release lock", n)

        # Lock 반환
        self._lock.release()

        logging.info("Thread %s: finishing update", n)




if __name__ == "__main__":
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")

    # 클래스 인스턴스화
    store = FakeDataStore()

    logging.info("Testing update. Starting value is %d.", store.value)

    # With Context 시작
    with ThreadPoolExecutor(max_workers=2) as executor:
        for n in ['First', 'Second', 'Third']:
            executor.submit(store.update, n)

    logging.info("Testing update. Ending value is %d.", store.value)

```

## 동기화 된 코드 (방식2)
```python
import logging
from concurrent.futures import ThreadPoolExecutor
import time
import threading


class FakeDataStore:
    # 공유 변수(value)
    def __init__(self):
        self.value = 0
        # Lock 선언
        self._lock = threading.Lock()

    # 변수 업데이트 함수
    def update(self, n):
        logging.info("Thread %s: starting update", n)

        # 뮤텍스 & Lock 등 동기화(Thread synchronization) 필요
        

        # Lock 획득(방법2)
        with self._lock:
            logging.info("Thread %s has lock", n)

            local_copy = self.value
            local_copy += 1
            time.sleep(0.1)
            self.value = local_copy

            logging.info("Thread %s about to release lock", n)
        
        logging.info("Thread %s: finishing update", n)




if __name__ == "__main__":
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO, datefmt="%H:%M:%S")

    # 클래스 인스턴스화
    store = FakeDataStore()

    logging.info("Testing update. Starting value is %d.", store.value)

    # With Context 시작
    with ThreadPoolExecutor(max_workers=2) as executor:
        for n in ['First', 'Second', 'Third']:
            executor.submit(store.update, n)

    logging.info("Testing update. Ending value is %d.", store.value)


```

```
02:27:06: Testing update. Starting value is 0.
02:27:06: Thread First: starting update
02:27:06: Thread First has lock
02:27:06: Thread Second: starting update
02:27:06: Thread First about to release lock
02:27:06: Thread First: finishing update
02:27:06: Thread Third: starting update
02:27:06: Thread Second has lock
02:27:07: Thread Second about to release lock
02:27:07: Thread Second: finishing update
02:27:07: Thread Third has lock
02:27:07: Thread Third about to release lock
02:27:07: Thread Third: finishing update
02:27:07: Testing update. Ending value is 3.
```


# Thread(5) - Prod and Cons Using Queue

- Keyword 1)Queue, 2)Python, Event 3)생산자 소비자 패턴(Producer/Consumer Pattern)


- producer는 생산자로 queue에 집어넣는 역할을 하고 consumer는 소비자로 queue에서 꺼내도록 하여, 동일한 작업을 하는 여러 개의 producer 혹은 consumer가 동시에 동작하도록 구성하여 병렬처리를 할 때 유용한 패턴입니다. 
- 파이썬의 경우에는 GIL(Global Interpreter Lock)이 존재하여 멀티 쓰레드보다는 멀티 프로세스로 구현을 해주어야 합니다.(정확한것인가???)     
- queue의 장점 : 실행중 컴퓨터가 꺼졌을때 큐가 없다면 쌓여진작업이 다 날라간다. 허나 큐는 큐에 저장되어 작업이 소멸되지 않아 작업을 이어갈수 있다(장애 예방 가능)
(참조:https://seokhyun2.tistory.com/53)   

<img src="https://i.imgur.com/Ef0333J.png" width="500px">

```
Producer-Consumer Pattern

(1).멀티스레드 디자인 패턴의 정석
(2).서버측 프로그래밍의 핵심
(3).주로 허리역할 중요

Python Event 객체
(1). Flag 초기값(0)
(2). Set() -> 1, Clear() -> 0, Wait(1 -> 리턴, 0 -> 대기), isSet() -> 현 플래그 상태
```



```python
"""
Section 1
Multithreading - Thread(5) - Prod and Cons Using Queue
Keyword - 생산자 소비자 패턴(Producer/Consumer Pattern)

"""
"""

Producer-Consumer Pattern
(1).멀티스레드 디자인 패턴의 정석
(2).서버측 프로그래밍의 핵심
(3).주로 허리역할 중요

Python Event 객체
(1). Flag 초기값(0)
(2). Set() -> 1, Clear() -> 0, Wait(1 -> 리턴, 0 -> 대기), isSet() -> 현 플래그 상태

"""

import concurrent.futures
import logging
import queue
import random
import threading
import time

# 생산자
def producer(queue, event):
    """네트워크 대기 상태라 가정(서버)"""
    
    while not event.is_set():
    # 생산을 set()메소드가 호출할때 까지 합니다. 
        message = random.randint(1, 11)
        logging.info("Producer got message: %s", message)
        queue.put(message)

    logging.info("Producer received event. Exiting")

# 소비자
def consumer(queue, event):
    """응답 받고 소비하는 것으로 가정 or DB 저장"""
    while not event.is_set() or not queue.empty():
        message = queue.get()
        logging.info(
            "Consumer storing message: %s (size=%d)", message, queue.qsize()
        )

    logging.info("Consumer received event. Exiting")

if __name__ == "__main__":
    # Logging format 설정
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO,
                        datefmt="%H:%M:%S")

    # 사이즈 중요
    pipeline = queue.Queue(maxsize=10)

    # 이벤트 플래그 초기 값 0
    event = threading.Event()

    # With Context 시작
    with concurrent.futures.ThreadPoolExecutor(max_workers=2) as executor:
        
        executor.submit(producer, pipeline, event)
        executor.submit(consumer, pipeline, event)
        #executor.submit(실행함수, 전달될 인자01(큐), 전달될 인자02(이벤트))
        
        # 실행 시간를 조정하지 않으면 바로 종료됨
        # 실행 시간 조정
        time.sleep(0.0001)

        # 실무에서는 while True를 활용한다(프로그램을 계속 실행되어야 하닌깐) 지금은 예제이니 time.sleep(0.1) 사용
        #while True:
        #    pass
        

        logging.info("Main: about to set event")
        
        # 프로그램 종료
        # flag 모두 0으로 변경됨
        event.set()

```

```
18:10:02: Producer got message: 9
18:10:02: Producer got message: 7
18:10:02: Producer got message: 2
18:10:02: Producer got message: 7
18:10:02: Producer got message: 1
18:10:02: Consumer storing message: 9 (size=3)
18:10:02: Producer got message: 9
18:10:02: Consumer storing message: 7 (size=3)
18:10:02: Producer got message: 10
18:10:02: Consumer storing message: 2 (size=3)
18:10:02: Main: about to set event
18:10:02: Producer got message: 3
18:10:02: Consumer storing message: 7 (size=3)
18:10:02: Producer received event. Exiting
18:10:02: Consumer storing message: 1 (size=3)
18:10:02: Consumer storing message: 9 (size=2)
18:10:02: Consumer storing message: 10 (size=1)
18:10:02: Consumer storing message: 3 (size=0)
18:10:02: Consumer received event. Exiting
```
