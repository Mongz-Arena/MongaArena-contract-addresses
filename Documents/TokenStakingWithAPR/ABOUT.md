# TimeLockedStakingWithAPR V1.0.0

## 개요
`TimeLockedStakingWithAPR`는 ERC-20 토큰을 스테이킹할 수 있는 Solidity 스마트 계약으로, 고정된 연간 이율(APR)을 제공합니다. 사용자는 사전에 정의된 기간 동안 토큰을 스테이킹하고, 스테이킹 금액과 APR에 따라 보상을 받을 수 있습니다. 이 계약은 강력한 관리 제어 기능과 스테이킹 및 보상에 대한 상세한 정보를 제공합니다.

---

## 주요 기능
1. **시간 제한 스테이킹**:
   - 스테이킹은 지정된 기간(`stakingStartTime`부터 `stakingEndTime`까지) 동안만 허용됩니다.

2. **고정 APR**:
   - 연간 이율(APR)은 계약 배포 시 설정되며, 보상 계산에 사용됩니다.

3. **동적 구성**:
   - 스테이킹 기간 및 APR은 계약 초기화 시 설정할 수 있습니다.

4. **보상 관리**:
   - 스테이킹된 토큰과 별도로 보상을 관리합니다. 관리자는 보상을 위한 토큰을 예치할 수 있습니다.

5. **스테이킹 일시 정지/재개**:
   - 관리자는 스테이킹을 일시 정지하거나 재개할 수 있습니다.

6. **상세한 상태 조회**:
   - 사용자와 관리자는 스테이킹, 보상, 참가자 세부 정보를 조회할 수 있습니다.

---

## 데이터 구조

### **변수**
- `stakingToken`: 스테이킹과 보상에 사용되는 ERC-20 토큰.
- `stakingStartTime`, `stakingEndTime`: 스테이킹 시작 및 종료 시간.
- `stakingDuration`: 스테이킹 기간(초 단위).
- `apr`: 보상 계산에 사용되는 연간 이율.
- `totalStakedAmount`: 현재 계약에 스테이킹된 총 토큰 양.
- `isPaused`: 스테이킹이 일시 중지 상태인지 나타내는 불리언 값.

### **구조체**
- `Staker`:
  - `stakedAmount`: 사용자가 스테이킹한 토큰 양.
  - `rewardEarned`: 스테이킹에 따라 계산된 보상.
  - `hasClaimed`: 사용자가 보상을 청구했는지 여부.

### **매핑**
- `stakers`: 각 사용자의 주소를 스테이킹 세부 정보에 매핑.

---

## 주요 함수

### 스테이킹
- `stake(uint256 amount)`: 사용자가 스테이킹 기간 시작 전에 토큰을 스테이킹할 수 있도록 허용.

### 보상 계산
- `calculateReward(address staker)`: APR과 스테이킹 기간을 기준으로 스테이커의 보상을 계산.

### 보상 청구
- `claim()`: 사용자가 스테이킹 종료 후 스테이킹한 토큰과 보상을 회수할 수 있도록 허용.

### 관리 기능
- `depositRewards(uint256 amount)`: 보상용 토큰을 계약에 예치하는 관리자 함수.
- `pauseStaking()` / `unpauseStaking()`: 스테이킹을 일시 중지하거나 재개.

### 조회
- `getStakerInfo(address staker)`: 사용자의 스테이킹 금액, 보상, 청구 상태를 반환.
- `getRewardAmount(address staker)`: 사용자의 예상 보상을 반환.
- `getStakingTokenBalance()`: 계약에 스테이킹된 총 토큰 양을 반환.
- `getRewardTokenBalance()`: 계약에 남아 있는 보상용 토큰 잔액을 반환.
- `getStakerCount()`: 고유 스테이커 수를 반환.
- `getStakerAddress(uint256 index)`: 특정 인덱스의 스테이커 주소를 반환.

---

## 보안 및 수정자
- **역할 기반 접근 제어**:
  - 관리자 함수는 `DEFAULT_ADMIN_ROLE`을 가진 사용자로 제한됩니다.
- **실행 조건**:
  - `onlyBeforeStakingStart`: 스테이킹 기간 시작 전에서만 실행.
  - `onlyAfterStakingPeriod`: 스테이킹 기간 종료 후에만 실행.
  - `whenNotPaused`: 스테이킹과 보상 청구는 일시 중지 상태가 아닐 때만 실행.