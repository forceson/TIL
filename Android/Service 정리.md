# Service 정리

Created: Jul 22, 2019 7:19 PM

Service는 백그라운드에서 오래 실행되는 작업을 수행할 수 있는 어플리케이션 컴포넌트이며 UI를 제공하지 않는다. 또 다른 어플리케이션 컴포넌트가 서비스를 실행할 수 있으며, 사용자가 또 다른 어플리케이션으로 전환하더라도 백그라운드에서 계속해서 실행된다. 컴포넌트를 서비스에 바인드하여 서비스와 상호작용할 수 있으며, IPC도 수행할 수 있다. 서비스는 앱에서 오직 1개의 인스턴스만을 생성할 수 있다.

**Foreground**

포그라운드 서비스는 사용자에게 알림을 줄 수 있는 기능을 수행한다. 예를 들면, 오디오 앱은 오디오 트랙을 플레이하기 위해 포그라운드 서비스를 사용한다. 포그라운드 서비스는 반드시 Notification을 표시해야 하며 사용자가 앱과 인터랙션이 발생하지 않아도 계속해서 작동한다.

**Background**

백그라운드 서비스는 사용자에게 직접적으로 알려지지 않는 기능을 수행한다. 앱이 스토리지와 관련된 기능을 수행하는 서비스를 사용한다면, 보통 그것은 백그라운드 서비스이다.

앱이 API 레벨 26 이상인 경우, 앱 자체가 포그라운드에 있지 않을 때 시스템이 백그라운드 서비스 실행에 제한을 건다. 이러한 경우, 앱은 scheduled job을 대체재로 사용해야한다.

**Bound**

앱 컴포넌트가 bindService()를 호출하여 해당 서비스에 바인드되면 서비스는 바인드 되었다고 한다. 바인드된 서비스는 클라이언트-서버 인터페이스를 제공하여 컴포넌트가 서비스와 상호작용하도록 해주며, 결과를 가져올 수도 있고, 이와 같은 작업을 여러 프로세스에 걸쳐 IPC로 수행할 수도 있다. 바인드된 서비스는 앱 컴포넌트가 해당 서비스에 바인드 되어 있을때만 실행된다. 여러 개의 컴포넌트가 서비스에 한꺼번에 바인드 될 수 있으나, 바인드된 모든 컴포넌트가 바인드를 해제하면 해당 서비스는 소멸된다.

서비스는 started, bound 두 가지 방식 모두로 작동가능하다. 서비스가 started 할 수도 있고 (나아가 무기한으로 실행) bound 될 수도 있다는 것이다. 이는 두 가지 콜백 메서드 onStartCommand(), onBind() 구현에 달려있는 문제이다.

서비스는 다른 앱에서도 사용가능한데 Manifest에서 비공개로 선언하여 외부 앱으로부터 차단도 가능하다.

서비스는 자신의 호스팅 프로세스의 main thread에서 실행되므로 서비스가 CPU를 많이 사용하는 작업을 수행하거나 CPU를 차단하는 작업을 수행할 경우, 서비스내에 새 쓰레드를 생성하여 따로 작업을 진행시켜 ANR을 방지해야한다.

## Manifest
```xml
    <manifest ... >
      ...
      <application ... >
          <service android:name=".ExampleService" />
          ...
      </application>
    </manifest>
```
자세한 정보는 [<service>](https://developer.android.com/guide/topics/manifest/service-element.html) 에서 확인.

앱의 보안을 보장하려면 서비스를 시작하거나 바인드할 때 항상 명시적 인텐트를 사용하고 인텐트 필터는 선언하지 않도록 한다.

유저가 돌아가고 있는 서비스를 인지하지 못하거나 신뢰하지 못하면 서비스를 중지시킬 수도 있기 때문에 description attribute를 적어주는 것이 좋다.

## 수명주기

![](https://developer.android.com/images/service_lifecycle.png)
```java
    public class ExampleService extends Service {
        int mStartMode;       // indicates how to behave if the service is killed
        IBinder mBinder;      // interface for clients that bind
        boolean mAllowRebind; // indicates whether onRebind should be used
    
        @Override
        public void onCreate() {
            // The service is being created
        }
        @Override
        public int onStartCommand(Intent intent, int flags, int startId) {
            // The service is starting, due to a call to startService()
            return mStartMode;
        }
        @Override
        public IBinder onBind(Intent intent) {
            // A client is binding to the service with bindService()
            return mBinder;
        }
        @Override
        public boolean onUnbind(Intent intent) {
            // All clients have unbound with unbindService()
            return mAllowRebind;
        }
        @Override
        public void onRebind(Intent intent) {
            // A client is binding to the service with bindService(),
            // after onUnbind() has already been called
        }
        @Override
        public void onDestroy() {
            // The service is no longer used and is being destroyed
        }
    }
```
액티비티와는 달리 서비스 수명 주기 콜백은 반드시 슈퍼클래스를 구현할 필요 없다.

Started Service와 Bound Servie는 서로 결합해서 사용할 수도 있는데, 대표적인 예로는 음악 재생 시 액티비티를 종료해도 음악이 나와야하는 경우는 Started Service이고 홈 화면에서 음악 재생 액티비티로 가면 현재 듣고 있는 음악에 대한 정보가 나와야 하는데 이 경우는 Bound Service이다. 또 네트워크로 파일을 다운로드 받고 있을 때 액티비티를 종료해도 다운로드는 계속되어야하므로 Started Service이고 다시 액티비티로 돌아왔을 때 다운로드 진행 상황을 보여줘야 하는 부분은 Bound Service이다.

## Started Service 생성

Started Service란 다른 컴포넌트가 startService()를 호출하여 시작하고, 그 결과로 서비스의 onStartCommand()가 호출되는 경우이다.

서비스가 시작되면 이를 시작한 컴포넌트와는 독립적인 자신만의 수명주기를 가지며 해당 서비스는 백그라운드에서 무기한으로 실행될 수 있다. 서비스는 작업이 완료되면 stopSelf()를 호출하여 스스로 알아서 중단하는 것이 정상이며 다른 컴포넌트가 stopService()를 호출하여 중단시킬 수도 있다.

다른 구성요소가 Intent를 통해 필요한 데이터를 담아 서비스를 시작할 수 있고, 전달된 Intent는 onStartCommand() 메서드에서 수신한다. 서비스가 사전에 작동중이라도 startService()를 호출하면 기존의 서비스가 호출되어 콜백인 onStartCommand() 메서드가 호출된다. 즉, 가장 처음 startService()를 호출하면 onCreate(), onStartCommand()가 호출되고 그 이후로는 onStartCommand()만 호출된다.
```java
    public class HelloService extends Service {
      private Looper mServiceLooper;
      private ServiceHandler mServiceHandler;
    
      // Handler that receives messages from the thread
      private final class ServiceHandler extends Handler {
          public ServiceHandler(Looper looper) {
              super(looper);
          }
          @Override
          public void handleMessage(Message msg) {
              // Normally we would do some work here, like download a file.
              // For our sample, we just sleep for 5 seconds.
              try {
                  Thread.sleep(5000);
              } catch (InterruptedException e) {
                  // Restore interrupt status.
                  Thread.currentThread().interrupt();
              }
              // Stop the service using the startId, so that we don't stop
              // the service in the middle of handling another job
              stopSelf(msg.arg1);
          }
      }
    
      @Override
      public void onCreate() {
        // Start up the thread running the service.  Note that we create a
        // separate thread because the service normally runs in the process's
        // main thread, which we don't want to block.  We also make it
        // background priority so CPU-intensive work will not disrupt our UI.
        HandlerThread thread = new HandlerThread("ServiceStartArguments",
                Process.THREAD_PRIORITY_BACKGROUND);
        thread.start();
    
        // Get the HandlerThread's Looper and use it for our Handler
        mServiceLooper = thread.getLooper();
        mServiceHandler = new ServiceHandler(mServiceLooper);
      }
    
      @Override
      public int onStartCommand(Intent intent, int flags, int startId) {
          Toast.makeText(this, "service starting", Toast.LENGTH_SHORT).show();
    
          // For each start request, send a message to start a job and deliver the
          // start ID so we know which request we're stopping when we finish the job
          Message msg = mServiceHandler.obtainMessage();
          msg.arg1 = startId;
          mServiceHandler.sendMessage(msg);
    
          // If we get killed, after returning from here, restart
          return START_STICKY;
      }
    
      @Override
      public IBinder onBind(Intent intent) {
          // We don't provide binding, so return null
          return null;
      }
    
      @Override
      public void onDestroy() {
        Toast.makeText(this, "service done", Toast.LENGTH_SHORT).show();
      }
    }
```
각 호출을 onStartCommand() 에서 처리하기 때문에 여러 개의 요청을 동시에 수행할 수 있다. 해당 예시는 그렇지 않으나, 그런 작업을 하려는 경우는 새 쓰레드를 하나씩 생성한 후 곧바로 수행하면 된다.

만약 디바이스의 메모리가 부족할 경우 안드로이드 시스템은 서비스를 강제 종료 시킬 수 있다. 이 때 서비스는 재시작할 수 있는데 이는 onStartCommand()의 리턴 값에 따라 달라진다.

**START_NOT_STICKY**

강제 종료 시, 재시작하지 않는다. 다만 전달할 PendingIntent가 있는 경우는 예외이다.

**START_STICKY**

강제 종료 시, 다시 onStartCommand()를 호출하되 Intent가 null로 전달된다. 다만 서비스를 시작할 PendingIntent가 있는 경우는 예외이다. 무기한으로 실행중이며 작업을 기다리고 있는 미디어 플레이어에 적합하다.

**START_REDELIVER_INTENT**

강제 종료 시, 재시작하되 Intent가 전달된다. 이는 즉시 재개되어야하는 작업을 능동적으로 수행 중인 서비스에 적합하다. 예를 들면 파일 다운로드 등이다.

서비스의 멀티쓰레딩이 필요한 경우가 아니라면 순차적으로 실행하는 IntentService를 이용하면 좋다.

서비스가 중복실행 되는 것을 방지하기 위해서는 isRunning 플래그를 두고 onStartCommand()에서 isRunning시 START_NOT_STICKY를 리턴하게 하면 된다.

### From Oreo

API 레벨 26 이상 부터는 앱이 포그라운드에 있지 않으면 백그라운드 서비스의 사용이 제한되는 내용이 업데이트 되었다. 따라서 앱은 포그라운드로 서비스를 생성해야하고 startForegroundService() 메서드를 통해 실행된다.

포그라운드 서비스는 PRIORITY_LOW와 함께 status bar notification을 보여줘야한다. 또한 notification을 보여주지 않아도 되는 서비스는 scheduled job이 사용이 권장된다.

또한 API 레벨 28 이상부터는 FOREGROUND_SERVICE 퍼미션 체크가 필수화 되었다.

따라서 다음과 같은 코드가 서비스에 추가되어야 한다.
```java
    Intent notificationIntent = new Intent(this, ExampleActivity.class);
    PendingIntent pendingIntent =
            PendingIntent.getActivity(this, 0, notificationIntent, 0);
    
    Notification notification =
              new Notification.Builder(this, CHANNEL_DEFAULT_IMPORTANCE)
        .setContentTitle(getText(R.string.notification_title))
        .setContentText(getText(R.string.notification_message))
        .setSmallIcon(R.drawable.icon)
        .setContentIntent(pendingIntent)
        .setTicker(getText(R.string.ticker_text))
        .build();
    
    startForeground(ONGOING_NOTIFICATION_ID, notification);
```
stopForeground()는 포그라운드에서 서비스를 제거한다.

## Bound Service 생성

바인딩하는 메서드는 다음과 같다.

public abstract boolean bindService(Intent service, ServiceConnection conn, int flags)

service는 대상 서비스, conn은 서비스와 연결되거나 끊겼을 때의 콜백, flags는 보통 BIND_AUTO_CREATE가 들어가는데, startService()를 통해 서비스를 실행하지 않았다면 넣어줘야한다. 해당 옵션을 주고 stopService() 호출 시 서비스는 종료되지 않는다. unbindService()를 바운드 된 곳마다 호출해야한다.

바인딩을 제공하는 서비스를 생성할 때는 클라이언트가 서비스와 상호작용하는 데 사용할 수 있는 프로그래밍 인터페이스를 제공하는 IBinder를 제공해야한다. 인터페이스를 정의하는 방법에는 세 가지가 있다.

### 바인더 클래스 확장

서비스를 사용하는 것이 로컬 어플리케이션뿐이고 여러 프로세스에 걸쳐 작동할 필요가 없는 경우, 커스터마이즈된 Binder 클래스를 구현하여 클라이언트로 하여금 서비스 내의 공개 메서드에 직접 액세스할 수 있도록 할 수도 있다.

1. 서비스에서 다음 중 한 가지에 해당하는 Binder의 인스턴스를 생성한다.
    - 클라이언트가 호출할 수 있는 공개 메서드 포함
    - 클라이언트가 호출할 수 있는 공개 메서드가 있는 현재 Service 인스턴스를 반환
    - 클라이언트가 호출할 수 있는 공개 메서드가 포함된 서비스가 호스팅하는 다른 클래스의 인스턴스를 반환
2. 이 Binder의 인스턴스를 onBind() 콜백 메서드에서 반환
3. 클라이언트의 경우, Binder를 onServiceConnected() 콜백 메서드에서 받아, 제공된 메서드를 사용해 바인딩된 서비스를 호출한다.
```java
    public class MainService extends Service {
    	//서비스에서 선언.
    	private final IBinder mBinder = new MainServiceBinder();
    	private ICallback mCallback;
    
    //서비스 바인더 내부 클래스 선언
    	public class MainServiceBinder extends Binder {
    	     MainService getService() {
    	         return MainService.this; //현재 서비스를 반환.
    	    }
    	}
     
      @Override
      public IBinder onBind(Intent intent) {
          // TODO: Return the communication channel to the service.
          //throw new UnsupportedOperationException("Not yet implemented");
          return mBinder;
      }
     
      //콜백 인터페이스 선언
      public interface ICallback {
          public void recvData(); //액티비티에서 선언한 콜백 함수.
      }
     
      //액티비티에서 콜백 함수를 등록하기 위함.
      public void registerCallback(ICallback cb) {
          mCallback = cb;
      }
     
      //액티비티에서 서비스 함수를 호출하기 위한 함수 생성
      public void myServiceFunc(){
          //서비스에서 처리할 내용
      }
    
    	public void doSomething() {
    			mCallback.recvData();
    	}
    }
```
```java
    public class MainActivity extends Activity {
     
    	//액티비티에서 선언.
    	private MainService mService; //서비스 클래스
     
    	//서비스 커넥션 선언.
    	private ServiceConnection mConnection = new ServiceConnection() {
          // Called when the connection with the service is established
          public void onServiceConnected(ComponentName className, IBinder service) {
              MainService.MainServiceBinder binder = (MainService.MainServiceBinder) service;
              mService = binder.getService(); //서비스 받아옴
              mService.registerCallback(mCallback); //콜백 등록
          }
     
          // Called when the connection with the service disconnects unexpectedly
          public void onServiceDisconnected(ComponentName className) {
              mService = null;
          }
      };
     
    	//서비스에서 아래의 콜백 함수를 호출하며, 콜백 함수에서는 액티비티에서 처리할 내용 입력
    	private MainService.ICallback mCallback = new MainService.ICallback() {
          public void recvData() {
     
    		    //처리할 일들..
     
          }
      };
     
    	//서비스 시작.
    	public void startServiceMethod(View v){
          Intent Service = new Intent(this, MainService.class);
          bindService(Service, mConnection, Context.BIND_AUTO_CREATE);
      }
    
    	public void doSomething() {
    			//액티비티에서 서비스 함수 호출
    		  mService.myServiceFunc();
    	} 
    }
```
### 메신저 사용

IPC를 수행해야하는 경우인데 멀티 쓰레딩을 수행할 필요는 없을 때, Messenger 인터페이스를 사용할 수 있다. 멀티쓰레딩일 경우 AIDL을 사용하는 것이 권장된다.

메신저 사용 방법

- 서비스가 클라이언트로부터 각 호출에 대해 콜백을 받는 Handler를 구현한다.
- Handler를 이용하여 Messenger 객체를 생성한다.
- Messenger가 IBinder를 생성하여 서비스가 onBind()로부터 클라이언트에게 반환하도록 한다.
- 클라이언트는 IBinder를 이용하여 Messenger를 인스턴스화하고, 이를 이용하여 Messenger 객체를 서비스에 전송한다.
- 서비스가 각 Message를 Handler로 수신한다. 구체적으로는 handleMessage() 메서드를 사용한다.

## IntentService

서비스의 멀티쓰레딩이 필요없을 때, Worker Thread를 사용하여 모든 요청을 순차적으로 실행한다. 해야 할 일은 onHandleIntent()를 구현하는 것이고 이는 각 시작 요청에 대한 인텐트를 수신하므로 개발자는 백그라운드 작업을 수행할 수 있다. 기본 반환 값은 START_NOT_STICKY이다.

- 애플리케이션의 기본 스레드와는 별도로, onStartCommand()에 전달된 모든 인텐트를 실행하는 기본 작업자 스레드를 생성한다.
- 한 번에 인텐트를 하나씩  onHandleIntent() 구현에 전달하는 작업 큐를 생성하므로, 다중 스레딩에 대해 염려할 필요가 전혀 없다.
- 시작 요청이 모두 처리된 후 서비스를 중단하므로 개발자가 stopSelf()를 호출할 필요가 전혀 없다.
- onBind()의 기본 구현을 제공하여 null을 반환하도록 한다.
- onStartCommand()의 기본 구현을 제공하여, 인텐트를 작업 큐로 보내고 그 다음은 onHandleIntent() 구현으로 보낸다.
```java
    public class HelloIntentService extends IntentService {
    
      /**
       * A constructor is required, and must call the super IntentService(String)
       * constructor with a name for the worker thread.
       */
      public HelloIntentService() {
          super("HelloIntentService");
      }
    
      /**
       * The IntentService calls this method from the default worker thread with
       * the intent that started the service. When this method returns, IntentService
       * stops the service, as appropriate.
       */
      @Override
      protected void onHandleIntent(Intent intent) {
          // Normally we would do some work here, like download a file.
          // For our sample, we just sleep for 5 seconds.
          try {
              Thread.sleep(5000);
          } catch (InterruptedException e) {
              // Restore interrupt status.
              Thread.currentThread().interrupt();
          }
      }
    }
```
### From Oreo

IntentService 대신 JobIntentService를 사용해야한다.

## 더 알아봐야 할 것

- JobIntentService - [링크](https://medium.com/til-kotlin-ko/android-o%EC%97%90%EC%84%9C%EC%9D%98-%EB%B0%B1%EA%B7%B8%EB%9D%BC%EC%9A%B4%EB%93%9C-%EC%B2%98%EB%A6%AC%EB%A5%BC-%EC%9C%84%ED%95%9C-jobintentservice-250af2f7783c)
- AIDL
- Binder
- Handler, Message

## 레퍼런스

[https://developer.android.com/guide/components/services#java](https://developer.android.com/guide/components/services#java)

[https://developer.android.com/guide/components/bound-services.html](https://developer.android.com/guide/components/bound-services.html)

[https://programmingfbf7290.tistory.com/entry/안드로이드-서비스-총정리스타티드바운드-서비스](https://programmingfbf7290.tistory.com/entry/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%EC%84%9C%EB%B9%84%EC%8A%A4-%EC%B4%9D%EC%A0%95%EB%A6%AC%EC%8A%A4%ED%83%80%ED%8B%B0%EB%93%9C%EB%B0%94%EC%9A%B4%EB%93%9C-%EC%84%9C%EB%B9%84%EC%8A%A4)
