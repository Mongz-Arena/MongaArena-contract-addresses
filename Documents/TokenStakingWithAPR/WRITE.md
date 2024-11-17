
# TimeLockedStakingWithAPR V1.0.0

## 개요
`TimeLockedStakingWithAPR`는 Solidity 스마트 계약으로, ERC-20 토큰을 스테이킹하고 고정된 연간 이율(APR)을 적용하여 보상을 받을 수 있는 시스템입니다. 사용자는 사전에 정의된 기간 동안 토큰을 스테이킹하며, 스테이킹 금액과 APR을 기준으로 보상을 받습니다. 이 계약은 강력한 관리 제어 기능과 스테이킹 및 보상 관련 상세 정보를 제공합니다.

---

## 주요 기능
1. **시간 제한 스테이킹**:
   - 스테이킹은 지정된 기간(`stakingStartTime`부터 `stakingEndTime`) 동안만 허용됩니다.

2. **고정 APR**:
   - 연간 이율(APR)은 계약 배포 시 설정되며, 보상 계산에 사용됩니다.

3. **동적 구성**:
   - 스테이킹 기간 및 APR은 계약 초기화 시 설정할 수 있습니다.

4. **보상 관리**:
   - 보상은 스테이킹된 토큰과 별도로 관리되며, 관리자가 보상 토큰을 예치할 수 있습니다.

5. **스테이킹 일시 정지/재개**:
   - 관리자는 필요에 따라 스테이킹을 일시 정지하거나 재개할 수 있습니다.

6. **상세한 상태 조회**:
   - 사용자와 관리자는 스테이킹, 보상, 참가자 세부 정보를 조회할 수 있습니다.

---

## 쓰기 함수

### **stake(uint256 amount)**

**설명**:
- 사용자가 스테이킹 기간 시작 전에 ERC-20 토큰을 스테이킹할 수 있도록 허용합니다.

**매개변수**:
- `amount`: 스테이킹할 토큰의 수량.

**로직**:
1. 스테이킹 기간이 시작되지 않았고, 스테이킹이 일시 정지 상태가 아님을 확인합니다.
2. 사용자의 토큰을 계약으로 전송합니다.
3. 사용자의 스테이킹 정보와 총 스테이킹 금액을 업데이트합니다.

**예외 조건**:
  1. **스테이킹 시작 이후 호출 시**:
     - 예외 메시지: `"Staking period has started"`
     - 원인: `block.timestamp`이 `stakingStartTime` 이후인 경우.
  2. **스테이킹이 일시 중지 상태일 때**:
     - 예외 메시지: `"Staking is paused"`
     - 원인: `isPaused`가 `true`인 경우.
  3. **토큰 전송 실패 시**:
     - 예외 메시지: `"Transfer failed"`
     - 원인: `stakingToken.transferFrom` 호출이 실패한 경우.

**조건**:
- `stakingStartTime` 이전에만 호출할 수 있습니다.
- 스테이킹이 일시 정지 상태가 아니어야 합니다.

**예시**:
```solidity
stake(1000);
```
- 사용자는 1000개의 토큰을 계약에 스테이킹합니다.

---

### **claim()**

**설명**:
- 사용자가 스테이킹 종료 후 스테이킹한 토큰과 보상을 회수할 수 있도록 허용합니다.

**로직**:
1. 스테이킹 기간이 종료되었는지 확인합니다.
2. 사용자의 스테이킹 금액과 APR을 기반으로 보상을 계산합니다.
3. 사용자의 스테이킹 금액과 보상을 반환합니다.

**조건**:
- `stakingEndTime` 이후에만 호출할 수 있습니다.
- 사용자가 스테이킹한 기록이 있어야 하고, 보상을 이미 청구하지 않았어야 합니다.

**예외 조건**:
  1. **스테이킹 종료 이전 호출 시**:
     - 예외 메시지: `"Staking period is not over yet"`
     - 원인: `block.timestamp`이 `stakingEndTime`보다 작은 경우.
  2. **스테이킹 기록이 없을 때**:
     - 예외 메시지: `"No staked tokens"`
     - 원인: `stakers[msg.sender].stakedAmount`이 `0`인 경우.
  3. **보상을 이미 청구했을 때**:
     - 예외 메시지: `"Rewards already claimed"`
     - 원인: `stakers[msg.sender].hasClaimed`가 `true`인 경우.
  4. **토큰 전송 실패 시**:
     - 예외 메시지: `"Staking token transfer failed"` 또는 `"Reward token transfer failed"`
     - 원인: `stakingToken.transfer` 호출이 실패한 경우.

**예시**:
```solidity
claim();
```
- 사용자는 스테이킹한 토큰과 계산된 보상을 회수합니다.

---

### **depositRewards(uint256 amount)**
**설명**:
- 관리자가 보상 지급을 위해 토큰을 계약에 예치할 수 있습니다.

**매개변수**:
- `amount`: 예치할 토큰의 수량.

**로직**:
1. 관리자가 토큰을 계약으로 전송합니다.

**조건**:
- `DEFAULT_ADMIN_ROLE`을 가진 사용자만 호출할 수 있습니다.

**예시**:
```solidity
depositRewards(5000);
```
- 관리자는 보상용으로 5000개의 토큰을 계약에 예치합니다.

---

### **pauseStaking()**

**설명**:
- 관리자가 스테이킹을 일시 정지할 수 있습니다.

**로직**:
1. `isPaused` 플래그를 `true`로 설정합니다.

**조건**:
- `DEFAULT_ADMIN_ROLE`을 가진 사용자만 호출할 수 있습니다.

**예시**:
```solidity
pauseStaking();
```
- 관리자가 스테이킹을 일시 정지합니다.

---

### **unpauseStaking()**

**설명**:
- 관리자가 스테이킹을 다시 활성화할 수 있습니다.

**로직**:
1. `isPaused` 플래그를 `false`로 설정합니다.

**조건**:
- `DEFAULT_ADMIN_ROLE`을 가진 사용자만 호출할 수 있습니다.

**예시**:
```solidity
unpauseStaking();
```
- 관리자가 스테이킹을 다시 활성화합니다.

