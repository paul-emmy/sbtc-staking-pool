# sBTC Staking Pool Smart Contract

A secure and feature-rich staking pool contract for sBTC tokens on the Stacks blockchain, implementing tiered rewards, delegation capabilities, and robust security measures.

## Features

- **Tiered Rewards System**

  - Base staking rewards
  - Tier 1: 10% bonus (30+ days staking)
  - Tier 2: 25% bonus (60+ days staking)

- **Security Measures**

  - Emergency withdrawal mechanism
  - Cooldown periods for withdrawals
  - Slashing mechanism for malicious actors
  - Admin controls and authorization
  - Pause functionality

- **Delegation System**
  - Stake delegation to other addresses
  - Delegation tracking and management

## Contract Constants

### Pool Configuration

- Minimum Deposit: 1,000,000 uSTX
- Maximum Pool Size: 1,000,000,000,000 uSTX
- Cooldown Period: 144 blocks (~24 hours)
- Reward Rate: 100,000 per block
- Slash Rate: 50%

### Staking Tiers

- Tier 1: 4,320 blocks (~30 days) - 10% bonus
- Tier 2: 8,640 blocks (~60 days) - 25% bonus

## Public Functions

### Staking Operations

#### `initialize()`

- Initializes the contract
- Can only be called once by authorized admin
- Sets initial timestamp

#### `deposit(amount: uint, token: sbtc-token-trait)`

- Deposits sBTC tokens into the staking pool
- Requirements:
  - Contract must be initialized
  - Pool not paused
  - Amount ≥ minimum deposit
  - Pool size < maximum size
  - No active cooldown period

#### `delegate-stake(delegate-to: principal)`

- Delegates staking rights to another address
- Cannot delegate to self

### Withdrawal Operations

#### `start-withdrawal(amount: uint)`

- Initiates withdrawal process
- Starts cooldown period
- Requirements:
  - Sufficient balance
  - Contract initialized
  - Pool not paused

#### `complete-withdrawal(amount: uint, token: sbtc-token-trait)`

- Completes withdrawal after cooldown period
- Requirements:
  - Cooldown period completed
  - Sufficient balance
  - Valid token contract

#### `emergency-withdraw(token: sbtc-token-trait)`

- Emergency withdrawal function
- Only available when emergency mode is active
- Bypasses cooldown period

### Administrative Functions

#### `slash-address(address: principal)`

- Slashes funds from malicious actors
- Admin only function
- Reduces stake by 50%

### Read-Only Functions

#### `get-user-info(user: principal)`

Returns:

```typescript
{
    deposit: uint,          // Current staked amount
    rewards: uint,          // Accumulated rewards
    staking-time: uint,     // Initial stake timestamp
    is-slashed: bool,       // Slashing status
    cooldown-end: uint      // Cooldown period end
}
```

#### `get-pool-info()`

Returns:

```typescript
{
    total-liquidity: uint,  // Total staked tokens
    total-rewards: uint,    // Total distributed rewards
    is-paused: bool,        // Pool pause status
    emergency-mode: bool,   // Emergency mode status
    current-time: uint      // Current block time
}
```

#### `get-delegation-info(delegator: principal)`

Returns:

```typescript
{
  delegate: principal; // Delegated address
}
```

## Error Codes

- `ERR_NOT_AUTHORIZED (u100)`: Unauthorized access
- `ERR_INSUFFICIENT_BALANCE (u101)`: Insufficient funds
- `ERR_INVALID_AMOUNT (u102)`: Invalid deposit/withdrawal amount
- `ERR_POOL_PAUSED (u103)`: Pool operations paused
- `ERR_ALREADY_INITIALIZED (u104)`: Contract already initialized
- `ERR_NOT_INITIALIZED (u105)`: Contract not initialized
- `ERR_SLASHING_CONDITION (u106)`: Slashing condition met
- `ERR_POOL_FULL (u107)`: Pool capacity reached
- `ERR_INVALID_DELEGATION (u108)`: Invalid delegation attempt
- `ERR_COOLDOWN_ACTIVE (u109)`: Withdrawal cooldown active
- `ERR_REWARD_UPDATE_FAILED (u110)`: Reward calculation failed

## Security Considerations

1. **Access Control**

   - Contract owner and pool admin have elevated privileges
   - Critical functions are protected by authorization checks

2. **Funds Protection**

   - Cooldown period prevents quick withdrawals
   - Emergency mode for critical situations
   - Slashing mechanism for malicious behavior

3. **State Management**
   - Pool can be paused for maintenance
   - Initialization can only occur once
   - Valid token checks on all operations

## Implementation Notes

1. **Reward Calculation**

   - Rewards are calculated based on stake amount and duration
   - Tier multipliers increase rewards for longer staking periods
   - Rewards are updated before any stake changes

2. **Withdrawal Process**

   - Two-step withdrawal process with cooldown
   - Emergency withdrawal bypasses cooldown
   - Automatic reward collection during withdrawal

3. **Delegation System**
   - Simple delegation model
   - Delegates can be changed at any time
   - No transfer of ownership, only staking rights

## Testing Recommendations

1. **Basic Functionality**

   - Deposit and withdrawal flows
   - Reward calculations
   - Delegation operations

2. **Security Features**

   - Authorization checks
   - Emergency procedures
   - Slashing mechanism

3. **Edge Cases**
   - Maximum pool size
   - Minimum deposit requirements
   - Cooldown period boundaries
