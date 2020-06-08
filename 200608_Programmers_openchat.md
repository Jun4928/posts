# Programmers Level2 오픈채팅방
## JavaScript Map

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/42888)

# 돌파구
결론적으로, 자바스크립트의 Map 자료구조를 사용하면 쉽게 풀린다.

처음에는 하나의 배열에 모든 record의 정보(command, id, name)를 담고, enter와 change 에서 같은 id로 이름이 바뀌는 경우에
배열을 순회해서 name을 갱신하도록 코드를 짰다. (하단의 링크 참조)

이렇게 구현했더니 id에 따라 이름을 바꾸는데 드는 비용이 최악의 경우에 O(n) 이었다. 그래서 결국 테스트케이스에서 속도저하로 통과하지 못했다.

가만 생각해보니, id에 따라 이름이 저장되는 자료구조로 Map이 있다. 나는 항상 key, value를 저장할 때 JavaScript의 Object를 사용했는데, 이번에는 Map을 이용해 봤다. MDN 문서에 따르면 잦은 key-value 쌍의 추가와 제거의 시나리오에 더 나은 성능을 제공한다고 한다. 그리고, 직접 key, value를 접근하지 않고 메소드를 통해서 접근하므로 더 안정성도 있고 직관적이다. [MDN JavaScript Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

따라서, users 라는 Map 자료구조를 만들고, set 메소드를 사용해서 id와 name을 key, value로 넣어주었다. id가 중복되는 경우에는 자동으로 value 값을 갱신해준다.

그리고 마지막에 메세지를 뿌려줄 때에는 해당 id의 name을 가져와야 하므로 Map 자료구조의 get 메소드를 사용했다.

# 구현
```javascript
const solution = (record) => {
  const info = [];
  const users = new Map(); 

  record.forEach( each => {
    const [command, id, name] = each.split(' '); // 로그 쪼개기
    if (command === 'Enter') { 
      users.set(id, name); // Map의 set 함수를 사용하면 중복되는 key값의 경우 알아서 value를 갱신하고, 그렇지 않을경우에 새 값을 집어 넣는다.
      info.push({id, message: '님이 들어왔습니다.'});
    }

    if (command === 'Leave') info.push({id, message: '님이 나갔습니다.' });

    if (command === 'Change') users.set(id, name);
  });

  return info.map((each) => { // info 에는 id와 message를 key로 하는 Object가 들어있다.
    return `${users.get(each.id)}${each.message}`; // 메세지를 뿌려줄 때, users Map 자료구조의 get으로 value: name 을 갖고와서 뿌려주면 된다.
  });
};

solution(["Enter uid1234 Muzi", "Enter uid4567 Prodo","Leave uid1234","Enter uid1234 Prodo","Change uid4567 Ryan"]);
const expected = ["Prodo님이 들어왔습니다.", "Ryan님이 들어왔습니다.", "Prodo님이 나갔습니다.", "Prodo님이 들어왔습니다."];
console.log(expected);
```

- [시행착오 코드 링크](https://github.com/Jun4928/Algorithms101/blob/master/programmers/Trials%20and%20Errors/openchat_trial.js)
