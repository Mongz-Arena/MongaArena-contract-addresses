
# TimeLockedStakingWithAPR V1.0.0

## 개요
`TimeLockedStakingWithAPR`는 Solidity 스마트 계약으로, ERC-20 토큰을 스테이킹하고 고정된 연간 이율(APR)을 통해 보상을 받을 수 있는 시스템입니다. 사용자는 미리 정의된 기간 동안 토큰을 스테이킹하고, 스테이킹 금액과 APR을 기준으로 보상을 받을 수 있습니다. 이 계약은 강력한 관리 제어 기능과 스테이킹 및 보상 관련 상세 정보를 제공합니다.

---

## 주요 기능
1. **시간 제한 스테이킹**:
   - 스테이킹은 지정된 기간(`stakingStartTime`부터 `stakingEndTime`까지) 동안만 허용됩니다.

2. **고정 APR**:
   - 연간 이율(APR)은 계약 배포 시 설정되며, 보상 계산에 사용됩니다.

3. **동적 구성**:
   - 스테이킹 기간 및 APR은 계약 초기화 시 설정할 수 있습니다.

4. **보상 관리**:
   - 보상은 스테이킹된 토큰과 별도로 관리됩니다. 관리자는 보상 지급을 위한 토큰을 예치할 수 있습니다.

5. **스테이킹 일시 정지/재개**:
   - 관리자는 필요에 따라 스테이킹을 일시 정지하거나 재개할 수 있습니다.

6. **상세한 상태 조회**:
   - 사용자와 관리자는 스테이킹, 보상, 참여자 세부 정보를 조회할 수 있습니다.

---

## 읽기 함수

### **getStakerInfo(address staker)**
- **설명**: 특정 스테이커의 스테이킹 세부 정보를 반환합니다.
- **매개변수**:
  - `staker`: 스테이커의 주소.
- **반환 값**:
  - `stakedAmount`: 스테이커가 스테이킹한 토큰 양.
  - `rewardEarned`: 계산된 보상 금액.
  - `hasClaimed`: 스테이커가 보상을 청구했는지 여부.
- **예시**:
  ```solidity
  getStakerInfo(0x123...);
  ```
  출력: `(1000, 25, false)`

---

### **getRewardAmount(address staker)**
- **설명**: 특정 스테이커의 예상 보상 금액을 반환합니다.
- **매개변수**:
  - `staker`: 스테이커의 주소.
- **반환 값**:
  - 예상 보상 금액.
- **예시**:
  ```solidity
  getRewardAmount(0x123...);
  ```
  출력: `25`

---

### **getStakingTokenBalance()**
- **설명**: 계약에 스테이킹된 총 토큰 양을 반환합니다.
- **반환 값**:
  - 스테이킹된 총 토큰 양.
- **예시**:
  ```solidity
  getStakingTokenBalance();
  ```
  출력: `5000`

---

### **getRewardTokenBalance()**
- **설명**: 계약에 남아 있는 보상용 토큰 잔액을 반환합니다.
- **반환 값**:
  - 남은 보상 토큰 잔액.
- **예시**:
  ```solidity
  getRewardTokenBalance();
  ```
  출력: `2000`

---

### **getTotalRewards()**
- **설명**: 모든 스테이커에게 지급될 총 보상 금액을 반환합니다.
- **반환 값**:
  - 지급될 총 보상 금액.
- **예시**:
  ```solidity
  getTotalRewards();
  ```
  출력: `500`

---

### **getTimeUntilStakingEnds()**
- **설명**: 스테이킹 종료까지 남은 시간을 반환합니다.
- **반환 값**:
  - 스테이킹 종료까지 남은 시간(초 단위). 이미 종료되었을 경우 `0`을 반환합니다.
- **예시**:
  ```solidity
  getTimeUntilStakingEnds();
  ```
  출력: `86400`

---

### **getStakerCount()**
- **설명**: 현재 스테이킹 중인 고유 스테이커 수를 반환합니다.
- **반환 값**:
  - 스테이커의 총 수.
- **예시**:
  ```solidity
  getStakerCount();
  ```
  출력: `15`

---

### **getStakerAddress(uint256 index)**
- **설명**: 특정 인덱스의 스테이커 주소를 반환합니다.
- **매개변수**:
  - `index`: 스테이커 주소 배열에서의 인덱스.
- **반환 값**:
  - 해당 인덱스의 스테이커 주소.
- **예시**:
  ```solidity
  getStakerAddress(0);
  ```
  출력: `0x123...`

---

### **getStakerAddressWithInfo(uint256 index)**
- **설명**: 특정 인덱스의 스테이커 주소와 상태 정보를 반환합니다.
- **매개변수**:
  - `index`: 스테이커 주소 배열에서의 인덱스.
- **반환 값**:
  - `stakerAddress`: 스테이커의 주소.
  - `stakedAmount`: 스테이킹한 토큰 양.
  - `rewardEarned`: 계산된 보상 금액.
  - `hasClaimed`: 보상을 청구했는지 여부.
- **예시**:
  ```solidity
  getStakerAddressWithInfo(0);
  ```
  출력: `(0x123..., 1000, 25, false)`

---

### **getStakingDuration()**
- **설명**: 스테이킹 기간(초 단위)을 반환합니다.
- **반환 값**:
  - 스테이킹 기간(초 단위).
- **예시**:
  ```solidity
  getStakingDuration();
  ```
  출력: `7776000`

---

### **getAPR()**
- **설명**: APR(연간 이율)을 반환합니다.
- **반환 값**:
  - APR(%) 단위.
- **예시**:
  ```solidity
  getAPR();
  ```
  출력: `10`


### **totalStakedAmount()**
- **설명**
- 계약에 스테이킹된 전체 토큰 양을 반환하는 public 변수입니다.
- **반환 값**
- 현재까지 스테이킹된 모든 토큰의 총합.
- **예시**:
  ```solidity
  totalStakedAmount;

---

### **stakingStartTime**

- **설명**: 스테이킹이 시작되는 시간을 반환하는 public 변수입니다.
- **반환 값**:
  - 스테이킹 시작 시간(유닉스 타임스탬프).
- **예시**:
  ```solidity
  stakingStartTime;
  ```
  출력: `1690000000`
    - 스테이킹이 2023년 7월 23일 14:40:00 UTC에 시작됩니다.
---

### **14. stakingEndTime**

- **설명**: 스테이킹이 종료되는 시간을 반환하는 public 변수입니다.
- **반환 값**:
  - 스테이킹 종료 시간(유닉스 타임스탬프).
- **예시**:
  ```solidity
  stakingEndTime;
  ```
  출력: `1692592000`
    - 스테이킹이 2023년 8월 20일 14:40:00 UTC에 종료됩니다.