# Unity의 Object Pooling 시스템

## 1. 개요

Object Pooling은 게임 개발에서 자주 사용되는 메모리 관리 기법. 자주 사용되는 객체를 미리 생성하여 pool에 보관하고, 필요할 때마다 이를 재사용하는 방식으로 동작함.

## 2. Object Pooling의 필요성

### 2.1 메모리 관리 측면

- 객체의 생성(Instantiate)과 제거(Destroy)는 많은 메모리 할당과 해제를 수반함. (프레임 드랍 유발)
- 잦은 메모리 할당/해제는 가비지 컬렉션을 유발하여 성능 저하를 일으킨다.
- Object Pooling은 이러한 메모리 단편화와 가비지 컬렉션 문제를 해결함.

### 2.2 성능 최적화 측면

- 실시간으로 객체를 생성하는 대신, 미리 생성된 객체를 활성화/비활성화하여 사용.
- 런타임 중의 성능 부하를 크게 감소시킵니다.
- 특히 모바일 게임과 MMORPG에서 중요한 최적화 기법.

## 3. 동작 원리

### 3.1 기본 구조

1. 초기화 단계

   - 필요한 객체들을 미리 생성.
   - 생성된 객체들을 비활성화 상태로 Pool에 보관.

2. 실행 단계
   - 객체가 필요할 때 Pool에서 비활성화된 객체를 가져옴.
   - 객체를 활성화하여 사용.
   - 사용이 끝난 객체는 비활성화하여 Pool로 반환.

### 3.2 구현 예시

```c#
public class BulletPool : MonoBehaviour
{
    private Queue<GameObject> bulletPool = new Queue<GameObject>();
    [SerializeField] private GameObject bulletPrefab;
    private int poolSize = 20;

    void Start()
    {
        // 초기 Pool 생성
        for (int i = 0; i < poolSize; i++)
        {
            GameObject bullet = Instantiate(bulletPrefab);
            bullet.SetActive(false);
            bulletPool.Enqueue(bullet);
        }
    }

    public GameObject GetBullet()
    {
        // Pool에서 총알 가져오기
        GameObject bullet = bulletPool.Dequeue();
        bullet.SetActive(true);
        return bullet;
    }

    public void ReturnBullet(GameObject bullet)
    {
        // 총알을 Pool로 반환
        bullet.SetActive(false);
        bulletPool.Enqueue(bullet);
    }
}
```

## 4. 적용 시 고려사항

### 4.1 Pool 크기 설정

- 동시에 존재할 수 있는 최대 객체 수를 고려하여 설정해야 함.
- 너무 큰 Pool은 불필요한 메모리 사용을 일으킬 수 있음.
- 너무 작은 Pool은 런타임 중 추가 생성이 필요할 수 있음.

### 4.2 메모리 관리

- Pool 내 객체들은 메모리에 계속 존재.
- 사용 빈도와 메모리 사용량을 고려한 적절한 Pool 크기 설정이 중요.

## 5. 결론

Object Pooling은 게임 개발에서 필수적인 최적화 기법. 특히 총알, 파티클 효과와 같이 빈번하게 생성/제거되는 객체들을 다룰 때 매우 효과적임. 적절한 Pool 크기 설정과 메모리 관리를 통해 게임의 성능을 크게 향상시킬 수 있음.
