# Multithreading

# 1.Python's GIL

```
- 파이썬에만 있는 독특한 특징 Gil(Global Interpreter Lock)
- Gil를 알고 있으면 파이썬의 멀티쓰레딩 프로그램 만들때 이해하기 유용하다.
 ```


## Gil(Global Interpreter Lock)
- 파이썬에서 쓰레드을 여러 개 생성한다고 해서 여러 개의 쓰레드가 동시에 실행되는 것은 아니다. 정확히 말하자면 두 개의 쓰레드가 동시해 실행되는 것처럼 보일 뿐, 특정 시점에서는 여러 개의 쓰레드 중 하나의 쓰레드만 실행된다.

<img src="https://i.imgur.com/1iKHUcJ.png" width="500px">

```
위 그림과 같이 thread를 동시에 실행하려고 해도 한 타임에 한 thread만 실행된다.
멀티스레드로 실행할때보다 하나의 스레드로 실행하는것이 성능이 더 낫다고해서 gill을 구현하였다고 한다.
```



## Gil 특징
### (1). py파일을 Cpython이 내부적으로 해석을 한다.(python파일을 bytecode로 변경)
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

### (2).CPython 메모리 관리가 취약하기 때문(즉, Thread-safe)

### (3).단일 스레드도 충분히 빠르다.
### (4).프로세스 사용 가능(Numpy/Scipy)등 Gil 외부 영역에서 효율적인 가능 코딩
### (5).병렬 처리는 Multiprocessing, asyncio 선택지 다양함.
### <del>(6).thread 동시성 완벽 처리를 위해서 -> Jython, IronPython, Stackless Python 등이 존재</del>








https://jeleedev.tistory.com/24    
https://m.blog.naver.com/alice_k106/221566619995


---




# 2.Thread(1) - Basic
- Keyword - threading basic
- Main Thread와 Sub(Child) Thread

***logging, threading, time는 파이썬 빌드인패키지이기때문에 파이썬에 내장되어 있다.***    
***스레드는 디버깅이 어렵기 때문에 logging패키징을 통해서 로그를 확인한다.(디버깅역할)***     
***if __name__ == "__main__" : => 메인영역이 실행되면 Main스레드가 자동으로 생성된다.***     
***.start()는 스레드 시작***     
***.join()는 서브스레드가 끝날때 까지 메인스레드가 대기한다.***      


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
03:37:44: Main-Thread : before creating thread
03:37:44: Main-Thread : before running thread
03:37:44: Sub-Thread First: starting
03:37:44: Main-Thread : wait for the thread to finish
03:37:44: Main-Thread : all done
03:37:47: Sub-Thread First: finishing

```

---

#Thread(2) - Daemon, Join
- Keyword - DeamonThread, Join

## DaemonThread(데몬스레드)

***(1). 백그라운드에서 실행*** 
***(2). 메인 스레드 종료시 즉시 종료()***
        - [2.Thread(1) - Basic]에서의 메인스레드 종료후 서브스레드가 종료되었다.
***(3). 주로 백그라운드 무한 대기 시 이벤트 발생시 실행하는 부분 담당***
        ->JVM(가비지 컬렉션), 워드,한글(자동저장),웹서버에서도 사용
```
  JVM(가비지 컬렉션)
   - 끝까지 남아서 사용하지 않는 메모리의 오브젝트의 주소 공간을 클리어해줌(메모리관리 효율적으로 해줌)
 
  문서 작성시 자동저장
   - 메인스레드 => 문서 작성 영역
   - 데몬스레드 => 문서 자동저장, 문서 종료시(저장이벤트출력) => 즉 메인스레드 종료시 데몬스레드 종료
```

***(4). 일반 스레드는 작업 종료시 까지 실행***

***x.isDaemon()*** 
- x스레드가 데몬스레드이면 true, 데몬스레드가 아니면 false 리턴

# 마지막 인자 Default= false이다.(데몬스레드 사용안함)
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




