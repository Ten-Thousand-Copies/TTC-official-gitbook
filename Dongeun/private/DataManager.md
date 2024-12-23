# Unity의 Data Manager 시스템

## 1. 개요
Data Manager는 게임 내의 다양한 데이터를 체계적으로 관리하는 시스템. 캐릭터 스탯, 아이템 정보, 스킬 데이터 등 게임의 핵심적인 수치와 정보들을 외부 파일로 관리하여 유지보수성과 확장성을 높인다.

## 2. Data Manager의 필요성

### 2.1 유지보수 측면
- 게임 밸런스 조정이 필요할 때 코드 수정 없이 데이터 파일만 수정할 수 있다.
- 앱스토어 심사 없이 서버를 통한 데이터 업데이트가 가능.
- 개발자가 아닌 기획자도 직접 데이터를 수정할 수 있다.

### 2.2 개발 효율성
- 하드코딩을 피하고 데이터 중심의 개발이 가능.
- 대량의 게임 데이터를 체계적으로 관리.
- 데이터 변경이 필요할 때 빌드 없이 즉시 테스트가 가능.

## 3. 데이터 포맷

### 3.1 JSON 형식
```json
{
    "characters": [
        {
            "id": 1,
            "name": "Warrior",
            "level": 1,
            "hp": 100,
            "attack": 10,
            "defense": 5
        }
    ]
}
```
- 간단한 구조의 데이터 관리에 적합
- 파싱 속도가 빠름
- 가독성이 좋음

### 3.2 XML 형식
```xml
<characters>
    <character>
        <id>1</id>
        <name>Warrior</name>
        <stats>
            <level>1</level>
            <hp>100</hp>
            <attack>10</attack>
            <defense>5</defense>
        </stats>
        <skills>
            <skill id="1" name="Slash" damage="20"/>
            <skill id="2" name="Block" defense="10"/>
        </skills>
    </character>
</characters>
```
- 복잡한 계층 구조의 데이터 관리에 적합
- 구조화된 데이터 표현이 용이
- 스키마를 통한 데이터 검증 가능

## 4. 구현 예시

### 4.1 데이터 구조 정의
```c#
[System.Serializable]
public class CharacterData
{
    public int id;
    public string name;
    public int level;
    public float hp;
    public float attack;
    public float defense;
}

public class DataManager : MonoBehaviour
{
    private Dictionary<int, CharacterData> characterDatabase = new Dictionary<int, CharacterData>();

    public void LoadCharacterData()
    {
        TextAsset jsonFile = Resources.Load<TextAsset>("Data/CharacterData");
        CharacterData[] characters = JsonUtility.FromJson<CharacterData[]>(jsonFile.text);
        
        foreach (var character in characters)
        {
            characterDatabase[character.id] = character;
        }
    }

    public CharacterData GetCharacterData(int id)
    {
        return characterDatabase.ContainsKey(id) ? characterDatabase[id] : null;
    }
}
```

## 5. 주의사항

### 5.1 데이터 보안
- 중요한 게임 데이터는 암호화하여 저장
- 서버 측 검증 구현 필요
- 치팅 방지를 위한 무결성 검사 구현

### 5.2 성능 최적화
- 필요한 데이터만 메모리에 로드
- 캐싱 시스템 구현
- 데이터 로드 시점 최적화

## 6. 결론
Data Manager는 현대 게임 개발에서 필수적인 시스템. 데이터 중심의 개발 방식을 통해 게임의 유지보수성을 높이고, 빠른 업데이트와 밸런스 조정을 가능하게 함. 특히 라이브 서비스 게임에서는 더욱 중요한 역할을 하며, 개발 효율성과 사용자 경험 향상에 크게 기여.