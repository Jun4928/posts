# Programmers Level2 방금 그 곡
## 문자열 처리(정규표현식), 예외처리

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/17683)

# 돌파구
이 문제에서는 사실 복잡한 자료구조라던가, 이 문제를 풀기위해 꼭 알아야 하는 알고리즘 같은 것은 없다. 
주어진 상황을 잘 이해하고, 문제 해결을 할 수 있는지를 확인하는 유형의 문제다. 나는 개인적으로 이런 문제의 유형을 좋아한다. 결국, 실무에서도 상황을 이해하고 문제해결을 하는 능력이 요구되기 때문이다. 

이 문제는 단순화 하면, 문자열 비교하는 문제다. 그렇지만, 이 문자열 비교를 하기 전에 조건에 따라 철저한 전처리 과정이 요구된다.

예를들어, 이 문제를 쉽게 해결하는 첫번째 단추는 악보코드의 간결화이다. C, C#, D, D# .. 이런 코드로 구성되어 있는 악보의 문자열을 단순화 해야 한다. 왜냐하면 C나 C# 이나 하나의 코드지만, 문자열에서 C#은 '두 칸'을 차지하기 때문이다.  
그래서 정규표현식과 replace 메소드를 사용해서 먼저 #이 붙어있는 코드들을 모두 소문자화 했다. 그래서 본래 코드와의 차별화를 했다. => 이렇게 문자열을 먼저 간단화 시켜놓고 나면 뒤에서 비교하는 로직에서 훨씬 더 매끄럽다.

두번쨰로 신경써야 할 로직은, 실제 연주된 시간에 따라 코드의 문자열을 만드는 함수를 구현하는 것이다. 주어진 시작시간과 끝난시간을 통해서 각 곡마다 실제 연주된 분(minitues)를 구할 수 있다. 문제의 조건에서는 1분에 코드 하나가 연주되기 때문에 실제 연주된 분에 따라 실제 연주된 코드를 구할 수 있다. 이 때 `연주된 분 / 곡의 코드 길이`를 나누어서 반복된 횟수를 구했고, 나누어 떨어지지 않는 나머지도 처리해서 실제 연주된 마지막 코드까지 처리를 해 주었다. 

마지막으로, 필터링을 거쳐서 연주된 코드안에 기억하고 있는 코드가 포함된다면 필터링을 거친 배열에 담는다.

예외처리를 끝까지 할 수 있는지 검증하기 위해서 카카오에서는 두가지 장치를 마련 해 두었는데, 필터링이 된 노래가 2개 이상일 때에는 다음과 같이 처리하도록 하였다.
1. 재생시간 긴 음악 제목 return
2. 재생시간도 같을 경우 먼저 입력된 음악 제목 return
이 요구사항은 JavaScript 의 Array 메소드인 sort 를 사용하면 쉽게 처리 할 수 있다.

# 구현
```javascript
const solution = function (m, musicInfos) {

  const chordSimplify = function (chord) { // #이 붙어있는 코드를 소문자화 시켜서 대문자 코드와 차별화 시킨다.
    return chord.replace(/C#/g, 'c')
                .replace(/D#/g, 'd')
                .replace(/F#/g, 'f')
                .replace(/G#/g, 'g')
                .replace(/A#/g, 'a');

  }
    
  const getPlayedChords = function(playedLength, chords) { // 연주된 시간에 따라 연주된 코드를 구하는 함수
    const repeated = Math.floor(playedLength / chords.length); // 반복된 횟수
    const restLength = playedLength % chords.length; // 나머지 부분

    let playedChords = '';
    for (let i = 1; i <= repeated; i++) playedChords += chords;

    return playedChords + chords.slice(0, restLength); // 반복된 부분 + 나머지 부분
  }

  const simplifiedM = chordSimplify(m);
  const playedSongs = musicInfos.map((musicInfo) => {
    const [start, end, title, chords] = musicInfo.split(',');
    const [startHour, startMiniute] = start.split(':');
    const [endHour, endMinitue] = end.split(':');

    const playedMinitues = (endHour - startHour) * 60 + (endMinitue - startMiniute); // 연주된 시간
    const simplifiedChords = chordSimplify(chords); // # 붙여진 코드를 간단화 하고

    const playedChords = getPlayedChords(playedMinitues, simplifiedChords); // 실제 연주된 코드를 구한다.
    return {title, playedChords, length: playedChords.length}; // filtering 거칠 때, 필요한 정보만 객체에 담아서 리턴한다.
  });

  const filteredSongs = playedSongs.filter((playedSong) => { // filtering 거치기
    const { playedChords } = playedSong;
    if (playedChords.includes(simplifiedM)) return true; // 연주된 곡에 기억된 코드가 있으면 true
    return false;
  });

  if (filteredSongs.length === 0) return "(None)"; // 없으면 none
  if (filteredSongs.length === 1) return filteredSongs[0].title; // 첫번째 title return

  // 필터링된 노래가 많을 경우에
  // 1. 재생시간 긴 음악 제목 return
  // 2. 재생시간도 같을 경우 먼저 입력된 음악 제목 return
  filteredSongs.sort((a, b) => { // 연주된 길이에 따라 내림차순으로 정렬한다.
    if (a.length < b.length) return 1;
    if (a.length > b.length) return -1;
    return 0;
  });

  return filteredSongs[0].title; // 첫번째 title return
};


// solution("ABCDEFG", ["12:00,12:14,HELLO,CDEFGAB", "13:00,13:05,WORLD,ABCDEF"]);
// const expected1 = "HELLO";

// const result2 = solution("CC#BCC#BCC#BCC#B", ["03:00,03:30,FOO,CC#B", "04:00,04:08,BAR,CC#BCC#BCC#B"]);
// console.log(result2);
// const expected2 = "FOO";

const result3 = solution("ABC", ["12:00,12:14,HELLO,C#DEFGAB", "12:14,12:29,HELLO2,CDEFGAB","13:00,13:05,WORLD,ABCDEF"]);
console.log(result3);
const expected3 = "WORLD";

```