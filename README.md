# Foundry DAO Governance Project

This project implements a decentralized autonomous organization (DAO) governance system using Solidity and the OpenZeppelin contracts. It demonstrates how on-chain governance can be implemented to control smart contracts through democratic voting processes.

## Project Overview

This project builds a complete DAO governance system with the following components:

1. A governance token (ERC20) that grants voting power
2. A governance contract with proposal, voting, and execution mechanisms
3. A timelock controller that enforces delays before execution
4. A target contract controlled by the governance system

## Architecture

The governance system follows a modular design with these key components:

### Governance Token (`GovToken.sol`)

- ERC20 token with voting capabilities
- Uses OpenZeppelin's ERC20Votes extension
- Allows token holders to delegate their voting power
- Tracks voting power at different points in time

### Governance Contract (`MyGovernor.sol`)

- Core governance functionality with proposal and voting logic
- Configured with specific parameters:
  - Voting Delay: 7200 blocks (~1 day at 12s blocks)
  - Voting Period: 50400 blocks (~1 week)
  - Proposal Threshold: 0 (anyone with voting power can create proposals)
  - Quorum: 4% of total supply must vote for a proposal to pass

### Timelock Controller (`TimeLock.sol`)

- Enforces a mandatory delay between proposal approval and execution
- Security measure to allow users to exit if undesirable actions are approved
- Configured with a minimum delay of 1 hour in tests

### Target Contract (`Box.sol`)

- Simple contract with state that can only be modified by the owner
- Ownership is transferred to the timelock controller
- Acts as the subject of governance

## Governance Flow

The complete governance process follows these steps:

1. **Proposal Creation**: A token holder creates a proposal specifying target addresses, values, and function calls
2. **Voting Delay**: After creation, a waiting period occurs before voting begins (1 day in production)
3. **Voting Period**: Token holders cast votes (for, against, abstain) during the voting period (1 week)
4. **Proposal Succeeded**: If quorum is reached and majority approves, the proposal succeeds
5. **Queueing**: The approved proposal is queued in the timelock controller
6. **Timelock Delay**: A mandatory waiting period before execution (1 hour in tests)
7. **Execution**: After the delay, anyone can execute the proposal

## Security Features

This governance system includes several security mechanisms:

1. **Timelock Delay**: Provides time for users to respond to potentially malicious proposals
2. **Role-Based Access Control**: The timelock uses distinct roles (proposer, executor, admin)
3. **Self-Administration**: The timelock can be configured for self-governance without external admin
4. **Quorum Requirements**: Ensures sufficient participation for legitimate governance
5. **Delegation**: Allows passive token holders to delegate voting power to active participants

## Governance Methods and Considerations

### On-Chain vs Off-Chain Governance

- **On-Chain**: This project implements fully on-chain governance, where proposals, votes, and execution happen directly on the blockchain
- **Off-Chain**: Alternative approaches include snapshot voting with on-chain execution, which reduces gas costs

### Voting Mechanisms

- **Token-Based Voting**: This project uses 1 token = 1 vote (plutocratic model)
- **Alternatives**:
  - Quadratic Voting: Voting power scales as square root of tokens, reducing wealth concentration
  - Conviction Voting: Voting power increases over time, favoring committed participants
  - Holographic Consensus: Combines predictions markets with voting for more efficient outcomes

### Tokenomics Considerations

While this project uses a simple ERC20 token model, production DAOs should consider:

- Token distribution mechanisms (fair launch, airdrops, etc.)
- Vesting schedules to align long-term incentives
- Vote delegation to improve participation rates
- Soulbound tokens or NFT-based governance for identity-based voting

### Limitations of Token-Based Governance

Token-based governance has known limitations:

1. Plutocracy concerns (wealth = power)
2. Voter apathy and low participation rates
3. Vulnerability to flash loan attacks
4. Difficulty in achieving sufficient decentralization

## Implementation Details

### Contract Extensions

The `MyGovernor` contract leverages several OpenZeppelin extensions:

- `GovernorSettings`: Configures core governance parameters
- `GovernorCountingSimple`: Implements basic vote counting logic
- `GovernorVotes`: Connects with the ERC20Votes token
- `GovernorVotesQuorumFraction`: Sets quorum as a percentage of total supply
- `GovernorTimelockControl`: Integrates with the timelock controller

### Timelock Role Management

The timelock controller uses role-based access control:

- `PROPOSER_ROLE`: Granted to the governor contract
- `EXECUTOR_ROLE`: Granted to address(0), allowing anyone to execute
- `DEFAULT_ADMIN_ROLE`: Initially granted to the deployer, should be renounced

### Testing

The project includes comprehensive tests demonstrating the complete governance flow:

1. Deploying the contracts
2. Creating a proposal
3. Voting on the proposal
4. Queueing the proposal in the timelock
5. Executing the proposal after the delay
6. Verifying the changes were applied to the target contract

## Getting Started

### Prerequisites

- [Foundry](https://book.getfoundry.sh/getting-started/installation.html)

### Installation

```bash
git clone <repository-url>
cd foundry-dao
forge install
```

### Running Tests

```bash
forge test
```

### Deployment

To deploy to a testnet or mainnet:

```bash
forge script script/DeployMyGovernor.s.sol --rpc-url $RPC_URL --private-key $PRIVATE_KEY --broadcast
```

## Resources for Further Learning

- [OpenZeppelin Governance Documentation](https://docs.openzeppelin.com/contracts/5.x/governance)
- [Vitalik Buterin's Blockchain Governance Blog](https://vitalik.eth.limo/general/2017/12/17/voting.html)
- [DAOstack Framework](https://daostack.io/)
- [Aragon DAO Platform](https://aragon.org/)
- [Compound Governance](https://compound.finance/governance)
