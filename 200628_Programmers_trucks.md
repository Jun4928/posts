# Programmers Level2 다리를 지나는 트럭
## JavaScript Stack, Queue  

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/42583)

# 돌파구

## 두개의 Queue
트럭이 다리를 건너야 하는데, 대기열이 존재한다. 대기열은 Queue 자료구조로 바로 매핑할 수 있다. 
FIFO(First in First Out)으로 가장 맨 앞에 있는 놈이 가장 먼저 처리되어야 한다.

다리를 건너는 트럭을 담는 자료구조도 Queue로 매핑할 수 있다. 왜냐하면 다리에 먼저 진입한 트럭이 가장 먼저 다리를 빠져나가기 때문이다.

따라서, 이 문제는 두개의 Queue를 이용해 풀 수 있다.
1. 다리에 진입하지 못하고 대기하고 있는 트럭을 담고있는 Queue: 함수의 세번째 인자로 들어오는 truck_weights
2. 현재 다리 위를 건너고 있는 트럭들을 담고 있는 Queue: `onBridge` 라는 변수명으로 선언했다.

JavaScript에서는 Queue를 Array로 구현할 수 있는데, 바로 `array.shift()` 메소드를 통해서 배열의 가장 앞의 원소를 빼낼 수 있고, 뒤에 넣을 때에는 `array.push()` 메소드로 가장 뒤에 넣을 수 있다.

## 다리를 건널 수 있는 조건
이 문제에서는 다리와 관련해서 두가지 조건이 주어진다.
1. bridge_length: 다리의 길이 = 다리를 건너는데 걸리는 시간(초단위)
2. weight: 견딜 수 있는 무게

즉 트럭이 다리위에 들어올 때 2가지 정보를 담고 있어야 한다. 트럭의 무게와 걸리는 시간. 두가지의 데이터를 하나에 담고싶다면? Object를 사용하면된다. 다리에 진입할 때에는 주어진 Input 배열 `truck_weights.shift()` 를 통해서 트럭의 무게를 저장하고, `bridge_length - 1`로 다리위에 진입하자 마자 1초가 감소하도록 했다. 예를들어, 2초가 걸리는 다리라면 진입하자마자 1초가 감소되고, 그 다음 초에 다리에서 빠져나갈 수 있도록 한다.

위의 두가지 정보를 담고있어야, 언제 트럭이 들어왔는지, 현재 다리위의 무게가 얼마나 되는지, 그리고 언제 트럭이 나가게 되는지를 매 번 1초 씩 증가할 때마다 알 수 있다.

## 모든 트럭이 다리를 다 건널때까지
1. `onBridge` Queue의 첫번째 트럭의 times 속성에 접근해서 0이 되어있으면 다리에서 내보낸다. 즉, 다리를 다 건넌 상황이라고 볼 수 있다.
2. 1초를 증가시키면서 현재 다리위에 올라와있는 트럭들의 무게를 모두 합하고, 1초가 흘렀으니 times 속성을 1씩 빼준다.
3. `현재 다리위의 무게 + 대기중인 버스의 무게`가 `다리가 감당할수 있는 무게` 보다 작거나 같으면 버스를 진입시킨다. 


# 구현
```javascript
const solution = function bridgeTrucks (bridge_length, weight, truck_weights) {
  let beforeBridgeLength = truck_weights.length; //  다리 지나기 전 트럭 개수
  let afterBridgeLength = 0; // 다리를 지나고 난 트럭 개수

  let seconds = 1;
  let onBridge = [{ truckWeight: truck_weights.shift(), times: bridge_length - 1 }];  
  // 진입 한 순간에 1초 감소

  // 다리를 지난 트럭의 개수와 건너기 전의 개수가 같을 때까지
  while (afterBridgeLength !== beforeBridgeLength) {
    console.log(seconds, onBridge); // 현재 시간과 다리에 있는 트럭들

    // 다리의 첫번째 있는 트럭의 시간이 다 흘렀으면 다리에서 dequeue
    if (onBridge[0].times === 0) {
      afterBridgeLength += 1; // 다리를 지난 트럭개수 증가
      onBridge.shift();
    }

    // ===== 1초 증가 할 때 로직 =====
    seconds += 1; 
    let currentWeight = 0; 
    onBridge = onBridge.map((truck) => {  // 현재 시점의 다리에 올라 와 있는 트럭의 무게를 더한다.
      const {truckWeight, times} = truck;
      currentWeight = currentWeight + truckWeight;
      return {...truck, times: times - 1}; // 모든 트럭의 걸리는 시간 1초식 감소
    });

    // 현재 다리위 무게 + 대기열 중 첫번째 트럭의 무게가 다리 weight 와 작거나 같아야
    // 트럭이 다리에 올라올 수 있다.
    if (currentWeight + truck_weights[0] <= weight) { 
      const truck = { truckWeight: truck_weights.shift(), times: bridge_length - 1 }; 
      // 진입한 순간에 1초 감소
      onBridge.push(truck); // 트럭 진입
    } 
  }

  return seconds; 
}


const result1 = solution(2, 10, [7, 4, 5, 6]);  // 8
console.log(result1);
// const result2 = solution(100, 100, [10]);  // 101
// console.log(result2);
// const result3 = solution(100, 100, [10,10,10,10,10,10,10,10,10,10]);  // 110
// console.log(result3);
```