# Spock 

- <i class="fa fa-user"></i>&nbsp;Daniel Hörner
- <i class="fa fa-calendar" aria-hidden="true"></i>&nbsp;13.12.2018
- <i class="fa fa-twitter" aria-hidden="true"></i>&nbsp;[@KuschelKrolik](https://twitter.com/KuschelKrolik)
- <i class="fa fa-bitbucket" aria-hidden="true"></i>&nbsp;[todo](https://TODO)

<--->

## Behaviour Driven Development

<-->
### Story: Returns go to stock

As a store owner
In order to keep track of stock
I want to add items back to stock when they're returned.

<-->
```gherkin
Scenario 1: Refunded items should be returned to stock
Given that a customer previously bought a black sweater from me
And I have three black sweaters in stock.
When they return the black sweater for a refund
Then I should have four black sweaters in stock.
```

<-->

```gherkin
Scenario 2: Replaced items should be returned to stock
Given that a customer previously bought a blue garment from me
And I have two blue garments in stock
And three black garments in stock.
When they return the blue garment for a replacement in black
Then I should have three blue garments in stock
And two black garments in stock.
```

<--->

## Spock vs. Junit

![white-spockVsJunit](resources/spockVsJunit.png)

<--->

## Hello Spock
```groovy
class HelloSpockSpec extends Specification {
  def "Should be able to remove from list"() {
    given:
        def list = [1, 2, 3, 4]
 
    when:
        list.remove(0)
 
    then:
        list == [2, 3, 4]
  }
}  
```

<--->

## Readable Errors

```groovy
Condition not satisfied:
 
list == [1, 3, 4]
|    |
|    false
[2, 3, 4]
 <Click to see difference>
```

<--->

## Data Driven Testing

<-->

### Value Array
```groovy
def "maximum of two numbers"() {
    expect:
    Math.max(a, b) == c

    where:
    a << [3, 5, 9]
    b << [7, 4, 9]
    c << [7, 5, 9]
  }
```

<-->

### Data Tables
```groovy
@Unroll
def "minimum of #a and #b is #c"() {
    expect:
    Math.min(a, b) == c

    where:
    a | b || c
    3 | 7 || 3
    5 | 4 || 4
    9 | 9 || 9
  }
```

<-->

### Readable Error - Again

```groovy
Condition not satisfied:
 
Math.min(a, b) == c
     |   |  |  |  |
     3   3  7  |  1
               false
 
Expected :1
 
Actual   :3
```

<--->

## Interaction Based Testing

<-->
### Mocks & Stubs & Spies
```groovy
def subscriberMock = Mock(Subscriber.class)

Subscriber subscriberStub = Stub()

Subscriber subscriberSpy = Spy{
    receive(_) >> "ok"
}
```

<-->
#### Simple
```groovy
subscriber.receive(_) >> "ok"
```

<-->
#### Sequences
```groovy
subscriber.receive(_) >>> ["ok", "error", "error", "ok"]
```

<-->
#### Complex
```groovy
def userRepository = Stub(UserRepository.class){
    saveCustomer(_, _) >> { String firstName, String lastName ->
        def newCustomer = new Customer(firstName, lastName)
        newCustomer.setFullName("$lastName, $firstName")
        newCustomer.setSince(LocalDate.now())
        return newCustomer
}
```

<-->
### Cardinality
```groovy
1 * subscriber.receive("hello")      // exactly one call
0 * subscriber.receive("hello")      // zero calls
(1..3) * subscriber.receive("hello") // between one and three calls
(1.._) * subscriber.receive("hello") // at least one call
(_..3) * subscriber.receive("hello") // at most three calls
_ * subscriber.receive("hello")      // any number of calls,
                                     // including zero
```

<-->
### Target & Method Constraint
```groovy
1 * subscriber.receive("hello") // a call to 'subscriber'
1 * _.receive("hello")          // a call to any mock object

1 * subscriber.receive("hello") // a method named 'receive'
1 * subscriber./r.*e/("hello")  // a method whose name matches 
                                // the given regular expression
```

<-->
### Arguments
```groovy

1 * subscriber.receive("hello")     // an argument is equal "hello"
1 * subscriber.receive(!"hello")    // an argument is unequal "hello"
1 * subscriber.receive()            // the empty argument list
1 * subscriber.receive(_)           // any single argument (with null)
1 * subscriber.receive(*_)          // any argument list (with empty)
1 * subscriber.receive(!null)       // any non-null argument
1 * subscriber.receive(_ as String) // any non-null argument String
1 * subscriber.receive({ it.size() > 3 }) // with given predicate
```

<-->
#### At Mock Creation Time
```groovy
Subscriber subscriber = Mock {
        1 * receive("hello") >> "ok"
        1 * receive("goodbye") >> "fail"
    }
```

<--->
MUCHO MERCI

