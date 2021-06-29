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

***logging threading,time는 파이썬 빌드인패키지이여서 파이썬에 내장되어 있다.***    
***스레드는 디버깅이 어렵기 때문에 logging패키징을 통해서 로그를 확인한다.(디버깅역할)***     
***.start()는 스레드 시작***     
***.join()는 서브스레드가 끝날때 까지 메인스레드가 대기한다.***      


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





```python
# 기초 Thread 생성 예제
# Main Thread vs Sub(Child) Thread


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

    #서브 스르ㅔ드 시작
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

    




