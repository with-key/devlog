# entity & repository
## Entity
- typeorm를 사용할 때는 class를 db table로 변환 시켜준다. 그래서 db로 변환할 class가 필요해지는데 그것을 entity라고 한다.
- 데코레이터
  - @Entity(): 해당 클래스가 entity임을 나타내는데 사용
  - @PrimaryGeneratedColumn(): 해당 열이 entity의 기본 키 열임을 나타는데 사용
  - @Column(): 해당 entity의 컬럼임을 나타내는데 사용

## Repository
- 엔티티 개체와 함께 작동하며, 엔티티 찾기, 삽입, 업데이트, 삭제등을 처리한다.
- Repository 패턴
  - 유저의 요청 -> 컨트롤러 -> 서비스 -> 레포짓토리로 처리하는 패턴
  - Repository는 db의 작업을 담당하여 처리한다.
  - @EntityRepository()를 사용해서 Entity를 Repository로 사용하는 것이다.
  - Repository를 다른 곳에서도 사용할 수 있도록 module에서 import 해야한다.