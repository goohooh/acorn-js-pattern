#5장 행동 패턴
--------------------------------
* 4장 구조패턴 : 객체 간 상호작용을 용이하게 구성해주는 패턴
* 5장 행동패턴 : 객체 간 데이터를 공유하는 방법 또는 객체 사이의 데이터를 교환하는 방법

* 5장의 주요 패턴
> 책임 연쇄 (Chain of responsibility)
> 명령 (Command)
> 해석자 (Interpreter)
> 반복자 (Iterator)
> 중재자 (Mediator)
> 메멘토 (Memento)
------------------------------------ 04/02 스터디
> 감시자 (Observer)
> 상태 (State)
> 전략 (Strategy)
> 템플릿 메소드 (Template method)
> 방문자 (Visitor)
------------------------------------ 04/09 스터디
--------------------------------------------------------------------

## 책임 연쇄 (Chain of responsibility)
#### 메세지가 클래스에서 다른 클래스로 전달되는 접근 방식을 기술.
 1. 클래스는 메세지를 처리하거나 체인에 있는 다음 멤버에게 전달함.
 2. 구현하기에 따라 메세지 전송에 몇 가지 다른 규칙을 적용할 수 있음.
 - ex) 체인의 첫번째 일치하는 링크만 메세지를 처리 또는 모든 일치하는 링크가 메세지를 처리할 수 있도록 처리
       링크 처리 중지, 변종 메세지 생성

### 구현
* 클래스의 단계별 일 처리를 생각하여서.. ( 우선 낮은 단계의 클래스가 일을 처리할 수 있도록, 해결하지 못하면 높은단계의 클래스로 패스 & 처리 과정의 연속)

````javascript
//모든 클래스(청취자)의 공통 인터페이스
//두가지 기능 (불만 듣기, 불만 해결 확인)
export interface ComplaintListener {
    IsAbleToResolveComplaint(complaint: Complaint): boolean;
    ListenToComplaint(complaint: Complaint): string;
}

//불만
//청취자 클래스는 이 객체를 받아, 듣고 해결할 수 있는지를 수행함
var Complaint = (function() {
    function Complaint() {
        this.ComplainingPart = "";
        this.ComplaintAbout = "";
        this.Complaint = "";
    }
    return Complatin;
})();

//ComplaintListener를 구체적으로 구현한 클래스 ( ClerkOftheCourt, King)
var ClerkOfTheCourt = (function() {
    function ClerkOfTheCourt() {}
    ClerkOfTheCourt.prototype.IsAbleToResolveComplaint = function (complaint) {
        // 법원 서기에 의해 해결될 수 있는 불만인지 결정
        return false;
    }

    ClerkOfTheCourt.prototype.ListenToComplaint = function (complaint) {
        // 몇가지 작업을 수행
        // 불만에 대한 해결책을 반환
        return '';
    }
})();
JudicialSystem.ClerkOfTheCourt = ClerkOfTheCourt;
var King = (function() {
    function King() {}
    King.prototype.IsAbleToResolveComplaint = function(complaint){
        return true;
    }
    King.prototype.ListenToComplaint = function(complaint){
        // 몇 가지 작업을 수행
        // 불만에 대한 해결책을 반환
        return '';
    }
})();
JudicialSystem.King = King;

//Resolver에서 해결할 수 있는 ComplaintListner를 찾는 작업을 하고 해당 클래스를 반환한다.
//해결할 수 있는 클래스가 나올 떄 까지 찾는 작업을 반복함
// ( 해결유무 확인 -> 해결할 수 있음 -> 해결)
//  (             -> 해결할 수 없음 ->     다음 객체 확인 -> 해결유무 확인 )
var ComplaintResolver = (function(){
    function ComplaintResolver() {
        this.complaintListeners = new Array();
        this.complaintListeners.push(new ClerkOfTheCourt());
        this.complaintListeners.push(new King());
    }
    ComplaintResolver.prototype.ResolveComplaint = function(complaint){
        for(var i =0; i < this.complaintListeners.length; i++){
            if(this.complaintListeners[i]).
            IsAbleToResolveComplaint(complaint){
                return this.complaintListeners[i].ListenToComplaint(complaint);
            }
        }
    };
    return ComplaintResolver;
})();

````
* 자바스크립트에서 Event가 책임 연쇄를 통해 전달됨.
개인적 생각.. 책임연쇄 행동패턴은 해당 클래스의 해결이 안되면 상위 클래스에서 해결함,
디폴트 클래스를 적은 기능을 해주고 상위 클래스에 큰 기능을 줌, 어떻게 보면 메모리를 적게도 아니면 많이도 사용할 수 있을거같고
어떤 클래스에서 문제 해결을 했느냐에 따라 요청의 수준을 알 수 있을것 같음 ( 내 생각엔 Exception 클래스도..?)

--------------------------------------------------
## 명령 패턴 (Command)
#### 메소드의 매개변수와 객체의 현재 상태 모두를 캡슐화하고 메소드를 호출하는 방법..?
 * 명령 패턴은 메소드를 나중에 호출 할 때 필요한 것들을 작은 패키지로 포장함.
 * 명령 실행 > ...( 명령 실행 코드 결정 대기) >

 *  구성 컴포넌트
    1) 클라이언트 ( 명령 생성, 전달(==호출자) )
    2) 명령
    3) 리시버  

-----------------------------------------------
> 명령 패턴 구성 컴포넌트들

### 코드 구현
1. 명령 메세지
 * 명령 설정 옵션
    1) 함수와 매개변수를 추적
        - 함수의 매개변수를 배열로 저장
````javascript
//명령 생성, 해당 명령을 구성하는 것들을 객체에 입력
var simpleCommand = new Array();
simpleCommand.push(new LordInstruction().BringTroops);
simpleCommand.push("King's Landing");
simpleCommand.push(500);
simpleCommand.push(new Date());

//1. 기본적인 명령 호출 방법
//1) 매개변수3개와  알맞는 명령을 주었을 때만 호출되도록
simpleCommand[0](simpleCommand[1], simpleCommand[2], simpleCommand[3]);
//2) 확장
simpleCommand[0](simpleCommand[1], simpleCommand[2], simpleCommand[3], simpleCommand[4], simpleCommand[5], simpleCommand[6]);
````
가장 기본적인 Command 패턴의 command 생성법
    * 명령 생성엔 실패하지 않음, 명령이 실행시 실패 판단됨(> 파라미터로 전달된 명령의 인자가 충족이 안된다고 생각해보기)
    * 이렇게 사용하는것 보다 클래스르 따로 생성해 올바른 인수 생성을 보장, 구분할 수 있도록 하는게 더 세련됨

```javascript
//2. 명령을 적절한 클래스로 구현
var BringTroopsCommand = (function (){
    function BringTroopsCommand(location, numberOftroops, when) {
        this._location = location;
        this._numberOfTroops = numberOfTroops;
        this._when = when;
    }
    BringTroopsCommand.prototype.Execute = function (){ //호출자!!(명령호출 Invoker, 명령 내부에 호출자가 선언된 케이스)
        var receiver = new LordInstructions();
        receiver.BringTroops(this._location, this._numberOfTroops,this._when);
    };
    return BringTroopsCommand;
})()
````
명령을 구체적으로 생성,
    * 매개변수가 정확한지 확인할 수 있음 > 명령이 생성시 실패할 수 있음 실행시 실패확률이 낮음
    * 명령은 메모리에 저장하여 호출하여 사용함.

2. 호출자 (Invoker)
 * 명령의 실행을 지시함 ( 시간이벤트, 사용자 인터렉션, 프로세스 다음단계 등 아무거나 상관 없음. Execute 코드!)
 * 즉시호출 또는 나중에 호출을 선택할 수 있음. ( 인기있는 방법은 이벤트 루프의 끝에 호출자를 선언하는것)
    ex)
````javascript
    process.nextTick(function(){command.Execute();});
````
 3. 수신자 (Receiver)
  * 명령을 실행함
  * 명령을 어떻게 수행할 지 알 수 있음(지연 또는 기타)
````javascript
var LordInstructions = (function(){
    function LordInstructions(){
    }
    LordInstructions.prototype.BringTroops = function(location, numberOfTroops, when){
        console.log("You have been instructed to bring" + numberOfTroops
                    + "troops to" + location
                    + "by" + when)
    }
    return LordInstructions;
})();
````

* 결론
 1) 클라이언트 : 명령 생성 및 전달 / 명령 / 수신자 : 명령 실행 및 전달
-------------------------------------------------------------------

## 해석자 (Interpreter) 패턴
 * 자신만의 고유한 언어를 생성할 수 있게 해주는 패턴 > 너 쓰고싶은 대로 만들어 써
     ex) SQL, 정규 표현식
 * 도메인 특화 언어(Domain Specific Languages) 시작 -> 하나의 요구사항에 특화던 언어를 만드는 것이 유용한 경우.    

### 예제
아래의 문법을 사용하여 전투몰록을 조회할 수 있는 코드를 작성하기.
 > (aggressor -> battle ground <- defender) -> victor  (공격자 -> 전쟁터 <- 방어자 ) -> 승자

보통 정규표현식을 준수함. (-> 렉서와 파서등 까지 도메인 특화 언어로 구성하는것은 좋은 방법이 아닌것 같음. >)
* cf) 렉서(lexor) : 코드 문자열을 작고 의미있는 조각(토큰)을 구분하는것 (ex. I like hambergur를 공백 단위로 구분함 > 공백이 렉서)
      파서(parser) : 렉서를 이용하여 나뉘어진 조각(문자열 또는 토큰)을 스트림으로 파싱해 최종 결과를 만드는 것.
-------------------------------------------------------------

1. 전투를 위한 자바스크립트 모델 생성
```javascript
var Battle = (function (){
    function Battle(battleGround, agressor, defender, victor) {
        this.battleGround = battleGround;
        this.agressor = agressor;
        this.defender = defender;
        this.victor = victor;
    }
    return Battle;
})()
```

2. 파서 생성
    전투 문자열을 받아서 nextBattle을 사용하여 파싱해 Battle() 구현.
```javascript
var Parser = (function() {
    function Parser(battleText) {
        this.battleText = battleText;
        this.currentIndex = 0;
        this.battleList = battleText.split("\n");
    }
    Parser.prototype.nextBattle = function (){
        if(!this.battleList[0]) return null;
        var segments = this.battleList[0].match(/\((.+?)\s?->\s?(.+?)\s?<-\s?(.+?)\s?->\s?(.+)/);
        return new Battle(segment[2], segments[1], segment[3], segment[4]);
    };
    return Parser;
})();

var text = "(Robert Baratheon -> River Trident <- RhaegarTargaryen) -> Robert Barateheon"
var p = new Parser(text);
p.nextBattle()
```
결과코드
> { battleGround: 'River Trident',
    agressor: 'Robert Baratheon',
    defender: 'RhaegarTargaryen',
    victor: 'Rober Baratheon'}

 많이 사용되지 않음. (사용 예 Less.)

* 문법 구조를 클래스화 한 코드 구조. 일련의 규칙으로 언어 해석을 함.
  규칙이 많아지면 복잡하고 무거워 지므로 그럴땐 파서&컴파일러를 사용하여라.
  SQL구문, Shell 커맨드 해석기, 통신 프로토콜.?

  ------------------------------

## 반복자
#### 집합에서 다음 항목을 순차적으로 선택하는 간단한 메소드를 제공해 주는 패턴

기본적 반복자 패턴의 인터페이스
```javascript
interface Iterator { next (); }
```  

### 코드 구현
```javascript
var KingSuccession = (function() {
    function KingSuccession(inLineForThrone) {
        this.inLineForThrone = inLineForThrone ;
        this.pointer = 0;
    }
    KingSuccession.prototype.next = function (){
        return this.inLineForThrone[this.pointer++];
    };
    return KingSuccession;
})();

var king = new KingSuccession(["Robert Baratheon", "Joffery Baratheon", "Tommen Baratheon"]);
king.next();
king.next();
king.next();

```

ex. 피보나치 수열 등 고정되어 있지 않은 집합을 반복하는 것도 있음.
```javascript
var FibonacciIterator = (function () {
    function FibonacciIterator() {
        this.previous = 1;
        this.beforePrevious = 1;
    }
    FibonacciIterator.prototype.next = function (){
        var current = this.previous + this.beforePrevious;
        this.previous = current;
        return current;
    };
    return FibonacciIterator;
})()
```
* 반복자 패턴은 생성된 모든 목록을 탐색할 수 있는 편리한 구조.

### ECMAScript6의 반복자
유용한 패턴이어서 ECMAScript6에서 표준으로 선택되었음.
* done과 value 매개변수를 포함하는 객체를 반환하는 메소드.
    - 반복자가 집합의 마지막에 도달했을 때 done 매개변수는 true가 됨.

```javascript
var kings = new KingSuccession(["Robert Baratheon", "Joffery Baratheon", "Tommen Baratheon"]);
for( var of kings){
    //next()
}
```

--------------------------
## 중재자(Mediator)
각 컨트롤은 페이지 내의 다른 컨트롤들을 수행하기 전에 해당 컨트롤이 유효한 지를 먼저 확인하고 싶음, 하지만 각 컨트롤에 대해 모두 알고 있어야 한다는 것은 악몽
= 객체 내의 다른 객체를 호출&실행 하기전에 해당 호출 객체가 유효한지 확인해야함, 하지만 각각 객체를 내부에 할당 또는 선언하는건 너무 많음.

* 컴포넌트의 중간에 위치해 메세지의 경로변경이 이뤄지는 유일한 장소로써 동작함.
    코드를 유지하는데 필요한 복잡한 작업을 단순화 시킴.
    개인적 생각으론 MVC 패턴의 컨트롤러 가 대표..

### 구현
```javascript
//메세지를 받고, 메세지를 전달하는 기능
var Karstark = (function(){
    function Karstark(greatLord){
        this.greatLord = greatLord;
    }
    Karstark.prototype.receiveMessage = function (message){
    };
    Karstark.prototype.sendMessage = function(message){
        this.greatLord.routeMessage(message);
    };
    return Karstark;
})();

var HouseStark = (function(){
    function HouseStark(){
        this.Karstark = new Karstark(this);
        this.bolton = new Bolton(this);
        this.frey = new Frey(this);
        this.umber = new Umber(this);
    };
    HouseStark.prototype.routeMessage = function(message){
    };
    return HouseStark;
})
```
 * 통신이 복잡하고 잘 정의돼 있을 때 효과적.

---------------------------
## 메멘토
#### 객체의 상태 정보를 저장 및 복원(rollback)하는 패턴
이전의 상태로 객체의 상태를 복원할 수 있는 방법을 제공. 복구가 힘든 명령을 쉽게 복원할 수 있음.

메멘토 패턴의 구성원
    * 발신자(originator) : 상태정보를 유지, 새로운 메멘토를 생성하는 인터페이스 제공
    * 관리자(caretaker) : 패턴의 클라이언트. 새로운 메멘토를 요청 및 복원 관리
    * 메멘토(memento) : 발신자의 저장된 상태의 표현. 롤백을 위해 스토리지에 저장하는 패턴.
    
------------------------------

## 감시자 (objserver)
 * 자바스크립트에서 가장 널리 사용되는 패턴 중 하나.
 * 단일 페이지 어플리케이션(SPA), MVVM 기능을 제공하는 라이브러리에서 큰 역할 (7장에 다시 나옴)
 * 객체의 값이 변할 떄 그 변화를 알고 싶을때 유용하게 사용됨.
 --------------------------------------------------------------------
 ```javascript
 var GetterSetter - (function(){
	function GetterSetter(){
	}
	GetterSetter.prototype.GetProperty = fucntion(){
		return this._property;
	};
	GetterSetter.prototype.SetProperty = function(value){
		this._property = value;
	};
	return GetterSetter;
 })();
 
 //값이 변할 때 마다 변경에 관심있는 다른객체(Listner 객체)를 호출할 수 있도록 확장함.
 Getter.prototype.SetProperty = function(value){
	var temp = this._property;
	this._property = value;
	this._listener.Event(value, temp);
};	//값이 변경되었음을 Listner에게 통보함(현재값, 이전값)
 ```
 
 위의 코드 확장이 감시자 패턴으로 연결됨.
 ### 구현
 ```javascript
 var Spy = (function (){
	function Spy() {
		this._partiesToNotify = [];
	}
	Spy.prototype.Subscribe = function(subscriber){
		this._partiesToNotify.push(subscriber);
	};
	
	Spy.prototype.Unsubscribe = function (subscriber) {
		this.partiesToNotify.remove(subscriber);
	};
	
	Spy.prototype.SetPainKillers = function (painKillers){
		this._painKillers = painKillers;
		for (var i =0; i< this._partiesToNotify.length; i++){
			this._partiesToNotify[i](painKillers);
		}
	};
	return Spy;
})();

//사용 예시
var Player = (function(){
	function Player(){
	}
	Player.prototype.OnKingPainKillerChange = function(newPainKiller){
		//추가 구현
	};
	return Player;
})();

//실행
var s = new Spy();
var p = new Player();
s.Subscribe(p.OnKingPainKillerChange);
s.SetPainKillers(12);
 ```
 
 감시자 패턴은 속성 뿐 아니라 메소드에서도 적용할 수 있음 > js플러그인 인프라를 제공하는 일반적인 방법.
 브라우저 Dom의 모든 이벤트 리스너는 감시자 패턴으로 구현됨. ex) 제이쿼리 라이브러리의 $("body").on("click","button",function(){	})
 감시자 패턴은 다른 형식으로 변경할 필요가 없는 좋은 패턴으로
 
 -------------------------------------------------
 ## 상태패턴(state pattern)
 * 유용한 장치이지만 자주 사용하지 않는다. > 사람들이 커다란 if문으로 구현을 자주 하니 이런 패턴 작성을 하지 않음

 //상태패턴을 사용지 않은 예제
 ```javascript
function (action,amount){
	if (this.state == "overdrawn" && action == "withdraw") {
		this.state = "on hold";		//상태 = 초과인출 & 행동 = 인출		> 상태 = 중지
	}
	if (this.state == "on hold" && action != "deposit"){
		this.state = "on hold";		//상태 = 중지 & 행동 = 예금을 제외한 모든것 	>그대로 중지
	}
	if(this.state == "good standing" && action =="withdraw" && amount <= this.balance) {
		this.balance -= amount;		//상태 = 정상 & 행동 = 인출 & 금액 <= 잔고		> 잔고 - 인출금
	}
	if(this.state == "good standing" && action =="withdraw" && amount > this.balance) {
		this.balance -= amount;		//상태 = 정상 & 행동 = 인출 & 금액 > 잔고		> 잔고 - 인출금, 상태 = 초과인출
		this.state = "overdrawn"
	}
	//이 외에도 더 많은 상태들 존재
};
 ```
 
 if 문 불록 대신에 상태 패턴을 사용하여서 변환	> 상태별로 클래스를 분류
 
 ### 구현
```javascript
var BankAccountManager = (function(){
	function BankAccountManager() {
		this.currentState = new GoodStandingState(this);
	}
	BankAccountManager.prototype.Deposit = function(mount) {
		this.currentState.Deposit(amount);
	};
	
	BankAccountManager.prototype.Withdraw = function(amount) {
		this.currentState.Withdraw(amount);
	};
	BankAccountManager.prototype.addToBalance = function(amount) {
		this.balance += amount;
	};
	BankAccountManager.prototype.getBalance = function(){
		return this.balance;
	};
	BankAccountManager.prototype.moveToState = function(newState){
		this.currentState = newState;
	};
	return BankAccountManager;
})();

//상태관리 클래스
var GoodStandingState = (function(){
	function GoodStandingState(manager){
		this.manager = manager;
	}
	GoodStandingState.prototype.Deposit = function(amount) {
		this.manager.addToBalance(amount);
	};
	GoodStandingState.prototype.Withdraw = function(amount){
		if(this.manager.getBalance() < amount) {
			this.manager.moveToState(new OverdrawnState(this.manager));
		};
		
		this.manager.addToBalance(-1*amount);
	}
	return GoodStandingState
})();

//초과인출 클래스
var OverdrawnState = (function(){
	function OverdrawnState(manager){
		this.manager = manager;
	}
	OverdrawnState.prototype.Deposit = function(amount){
		this.manager.addToBalance(amount);
		if(this.manager.getBalance() > 0 {
			this.manager.moveToState(new GoodStandingState(this.manager))
		}
	}
	OverdrawnState.prototype.Withdraw = function (amount) {
		this.manager.moveToState(new OnHold(this.manager));
		throw "Cannot withdraw money from an already overdrawn bank account"
	};
	return OverdrawnState;
	})();
	
//정지 클래스
var onHold = (function () {
	function onHold(manager) {
		this.manager = manager;
	}
	OnHold.prototype.Deposit = function(amount){
		this.manager.addToBalance(amount);
		throw "Your account is on hold and you must go to the bank to resolve the issue";
	};
	OnHold.prototype.Withdraw = function(amount) {
		throw "Your account is on hold and you must go to the bank to resolve the issue";
	};
	return OnHold;
})();
``` 

코드를 캡슐화 해서 장기적으로 사용. 상태 클래스 사용할 기회는 아주 많음.
상태 전환이 복잡하다면 클래스를 래핑해서 사용하면 쉽게 사용 가능. (많이 짜봐야지 앎)
ex.) goodStandingState.on("withdraw").when(fucntion(manager){return manager.balance > 0}.transitionTo("goodStandingState").when(function(manager){return manager.balance <= 0;}).transitionTo("Overdrawn");

-----------------------------------------------

## 전략패턴
목적 달성을 위하여 필요한 전략 패턴들을 투명하게 교체하여 (동일인터페이스) 적재적소에 맞는 클래스를 사용할 수 있또록 함.

###전략패턴 선택 종류
> 1) 정적 전략 선택 : 전략이 자주 변경되지 않거나 단일 고객을 위한 특정한 전략이 경우 가장 적합함( 구성변수 또는 하드코딩으로도 구현 )
> 2) 데이터세트에 분석 : 데이트세트에 대한 분석을 실행하여 가장 적합한 전략을 찾음 (ex. 전략a가 전략b보다 좋다고 알려져 있으면 분석을 위한 빠른 알고리즘을 실행하여 전략을 선택)
>                        api호출에 사용됨. 폴백에도
> 3) 점진적인 향상 : 사용자에게 신속한 피드백을 제공하기위하여 빠르고&부정확한 알고리즘 실행 > 동시에 느리고&가장 정확한 알고리즘 완려시 더 정확한 결과로 기존 결과 대체함 (ex. gps)
> 4) 무작위 전략 : 서로 다른 두개의 전략의 성능을 비교하는 경우 유용. A/B 테스팅의 기반

### 구현
```javascript
export interface ITravelMethod{
	Travel(source: string, destination: string} : TravelResult;
}	//트레블 인터페이스 생성시 출발&목적지 > 결과 리턴

//예제코드
var TravelResult = (function(){
	function TravelResult(durationInDays, probabilityOfDeath, cost){
		this.durationInDays = durationInDays;
		this.probabilityOfDeath = probabilityOfDeath;
		this.cost = cost;
	}
	return TravelResult;
})();
Travel.TravelResult = TravelResult;

//전략 1, 2 ,3, 4
var SeaGoingVessel = (function(){
	function SeaGoingVessel(){
	}
	SeaGoingVessel.probabilityOfDeath.Travel = function (source, destination){
		return new TravelResult(15, .5, 500);
	};
	return SeaGoingVessel;
})();

var Horse = (function(){
	function Horse() {
	}
	Horse.prototype.Travel = function(source, destination){
		return new TravelResult(30,.25,50);
	};
	return Horse;
})();

var Walk = (function(){
	function Walk(){}
	Walk.prototype.Travel = function (source,destination){
		return new TravelResult(150,.55,0);
	}
	return Walk;
})();
```

전략 패턴의 전통적인 구현에서는 각 전략에 대한 메소드 시그니쳐가 동일함.
-----------------------------------------------------------

## 템플릿 메소드 패턴
 알고리즘의 일부분은 공유하고, 일부분만 다른 방법으로 구현하는 접근방식. (대부분이 알고리즘이 동일하고 조금만 다를 경우 전략으로 다 대체하는게 부담 > 템플릿 메소드 사용함.)
 템플릿 메소드 패턴은 알고리즘의 점진적 향상의 원칙을 적용할 수 있게 해줌. 
 추상역역으로 남겨진 재정의 메소드를 구현하면서 점진적 확장.
 
### 구현
//맥주제조
```javascript
var BasicBeer = (function(){
	function BasicBeer(){
	}
	BasicBeer.prototype.Create = function () {
		this.AddIngredients();
		this.Stir();
		this.Ferment();
		this.Test();
		if(this.TestingPassed()) {
			this.Distribute();
		}
	};
	
	BasicBeer.prototype.AddIngredients = function() {
		throw "Add ingredients needs to be implemented";
	};
	BasicBeer.prototype.Stir = function(){
		//~~
	};
	BasicBeer.prototype.Ferment = function (){
		//~~
	};
	BasicBeer.prototype.Test = function(){
	}
	BasicBeer.prototype.TestingPassed = function(){
		throw "Conditions to pass a test must be implmented";
	};
	BasicBeer.prototype.Distribute = function(){
	};
	return BasicBeer;
})();

//위의 템플릿을 상속받는 구현코드 
```javascript
var RaspberryBeer = (function(_super){
	_extends(RaspberryBeer, _super);
	function RaspberryBeer(){
		_super.apply(this, arguments);
	}
	RaspberryBeer.prototype.AddIngredients = function(){
		//구현
	};
	RaspberryBeer.prototype.TestingPassed = function(){
		//구현코드
	};
	return RaspberryBeer;
})(BasicBeer);
```
상속을 사용하기 때문에 부모 클래스와 강하게 결합되어 잇음. 바람직 하진 않음.

------------------------------------

## 방문자 (Visitor)
 알고리즘을 객체의 구조와 분리하는 방법을 제공함.
 유형이 다른 객체의 집합들 사이에서 유형에 따라 다른 동작을 수행하고 싶다면 일반적으로 큰 if문 필요가
 
 //유형에 따라 다른동작을 위해
 ```javascript
 interface IMemberOfArmy{
	printName();
}

 //위의 인터페이스를 간단 구현
 var Knight = (function(){
	function knight() {
	}
	Knight.prototype.printName = function(){
		console.log("Knight");
	}
	return Knight;
})();	//이 외의 다른 객체들도 존재한다 가정함

```

printName 함수 호출하기
```javascript
var collection = [];
collection.push(new Knight());
collection.push(new FootSoldier());
collection.push(new Lord());
collection.push(new Archer());

for (var i =0; i < collection.length; i++){
	if(typeof (collection[i]) == 'Knight')
		collection[i].printName();
	else
		console.log("Not a knight");
}

> 결과값
 Not a Knight
 Not a Knight
 Not a Knight
 Not a Knight		//typeof는 모든 경우의 객체를 반환하기 떄문에 결과가 이렇게나옴 instanceof 사용하는 것이 바람직.

 var collection = [];
collection.push(new Knight());
collection.push(new FootSoldier());
collection.push(new Lord());
collection.push(new Archer());

for (var i =0; i < collection.length; i++){
	if((collection[i]) instanceof 'Knight')
		collection[i].printName();
	else
		console.log("No match");
}		//누군가 object.create 사용전 까진 잘 작동함
 ```
 
 자바스크립트는 메소드의 재정의를 지원하지 않음 > 문제점이 뭘까요
 핵(hack)을 사용하기 보단 메소드를 다른이름으로 만들어서 사용하는 것이 좋음.
  #### 서로를 확실하게 구분하는 방법으로 방문자 패턴 사용 하길 권장. > 각각의 형태를 나타내는 변수를 클래스에 정의하는 방법.!
  
```javascript
var Knight = (function () {
	function Knight() {
		this._type = "Knight"		//_type을 "Knight로 정의"
	}
	Knight.prototype.printName = function(){
		console.log("Knight");
	};
	return Knight;
})();

collection.push(new Knight());
collection.push(new FootSoldier());
collection.push(new Lord());
collection.push(new Archer());

for(var i=0; i = collection.length; i++){
	if(collection[i]._type =='Knight')	//_type으로 메소드 재정의를 지원하는 것 처러 만들 수 있음.
		collection[i].printName;
	else
		console.log("No match");
}
```

* 새로운 변수로 메소드 재정의 지원하는것 처러 ㅁ만들어 방문자 패턴 구현해보기
```javascript
//1) visitor 받아, 이를 적용하는 일반적 메소드로 군대 멤버들을 확장함.

var Knight = (function(){
	function Knight() {
		this._type = "knight";
	}
	Knight.prototype.printName = function (){
		console.log("Knight");
	};
	Knight.prototype.visit = function(visitor){
		visitor.visit(this);
	};
	return Knight;
})();
//2) 방문자 구축

var SelectiveNamePrinterVisitor = (function() {
	function SelectiveNamePrinterVisitor(){
	}
	SelectiveNamePrinterVisitor.prototype.Visit = function(memberOfArmy){
		if(memberOfArmy._type =="Knight"){
			this.VisitKnight(memberOfArmy);
		}else{
			console.log("Not a Knight");
		}
	};
	
	SelectiveNamePrinterVisitor.prototype.VisitKnight = function(memberOfArmy) {
		memberOfArmy.printName();
	};
	return SelectiveNamePrinterVisitor;
})();

//3) 사용

var collection = [];
collection.push(new Knight());
collection.push(new FootSoldier());
collection.push(new Lord());
collection.push(new Archer());

var Visitor = new SelectiveNamePrinterVisitor();

	for(var i =0; collection.length; i++){
		collection[i].visit(visitor);
	}	//컬렉션의 항목이 무엇인지에 대한 결정을 방문자로 미룸.