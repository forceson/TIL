# by를 이용한 delegation 패턴

Created: Nov 04, 2019 11:19 PM

## 델리게이션

델리게이션(Delegation)은 패턴으로 위임자(Delegator)가 실행되면 이곳에서 적절한 업무 처리가 되어야 하는데 이 처리를 위임자에서 직접 처리하지 않고 위임대행자(delegatee)에게 맡긴다는 개념이다.

예를 들어 위임자가 처리해야 하는 업무가 데이터 저장이라고 할 때, 데이터가 하나의 방법으로만 저장되는 것이 아니라 파일, 데이터베이스, 네트워크 등에 저장할 수도 있다고 가정하면 각각의 데이터 저장 로직을 구현한 대행자를 만들고, 그 대행자가 데이터를 저장하도록 처리할 수 있다.
```kotlin
    interface Updater {
        fun update(str: String)
    }
    
    class FileUpdater: Updater {
        override fun update(str: String) {
            println("i am file delegatee : $str")
        }
    }
    
    class DbUpdater: Updater {
        override fun update(str: String) {
            println("i am db delegatee : $str")
        }
    }
    
    class RemoteUpdater: Updater {
        override fun update(str: String) {
            println("i am file delegatee : $str")
        }
    }
    
    class UpdateDelegator(obj: Updater) : Updater by obj
    
    class UpdateDelegator2(private val obj: Updater) {
        fun update(str: String){
            obj.update(str)
        }
    }
    
    fun main(args: Array<String>) {
        val dbUpdater = DbUpdater()
        UpdateDelegator(dbUpdater).update("dbdb")
    }
```
위와 같이 Delegator와 Delegatee를 만들어 줄 수 있는데 주목할 부분은 UpdateDelegator이다. by 키워드를 이용하면 인터페이스(Updater)의 함수가 by 오른쪽에 작성한 객체(obj)의 함수를 호출하도록 작성해 자동으로 UpdateDelegator에 추가되게 한다. 결국, 일일이 위임자를 작성할 필요가 없다는 것이다.

위임자 클래스에서 대행자를 호출하는 구문이 by에 의해 자동으로 작성된다는 개념인데, 이를 위해서는 약간의 규칙에 맞게 인터페이스, 클래스들을 준비해 주어야 한다. 위임자와 대행자가 같은 인터페이스를 구현하고 있어야 만이 by를 이용하여 위임 패턴을 구현할 수 있다.

### 어댑터 패턴과의 차이?

구조적으로는 매우 동일하다. 그러나 목적에 차이가 있다. 어댑터 패턴은 다른 여러가지를 같은 API로 사용하려는게 주목적이다. 반면, 위임 패턴은 원래 본인이 수행해야 하는 일정 정도의 업무를 다른 곳에 위임하기 위한 것이 주목적이다.