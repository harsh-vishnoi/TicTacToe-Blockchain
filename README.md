# Tic-Tac-Toe on Ethereum

Two players stake 0.1 ether each and play a 3×3 game in a Solidity contract. The winner receives the pot. A draw splits it. A turn that is not taken within three minutes can no longer place a stone.

The client in `app/` reads the board and sends moves through Web3 and MetaMask.

## Contract

`contracts/TicTacToe.sol` (Solidity 0.5.12).

| Action | Rule |
| --- | --- |
| Deploy with `0.1 ether` | The deployer becomes player 1 |
| `joinGame` with `0.1 ether` | A different address becomes player 2 and the game starts. Player 2 moves first |
| `setStone(x, y)` | The active player occupies an empty cell. `x` and `y` are 0, 1, or 2 |
| Win | Three owned cells in a row, column, or either diagonal. The winner is paid the contract balance |
| Draw | The board fills with no winner. Each player receives half |
| Timeout | `setStone` reverts once three minutes have passed since the last action |

Events: `PlayerJoined`, `NextPlayer`, `GameWin`, `GameDraw`, `PayoutSuccess`. `getBoard` returns the 3×3 array of addresses.

An `emergencyCashOut` function is present only as a comment. A timed-out game does not pay anyone back by itself.

## Layout

| Path | What it is |
| --- | --- |
| `contracts/TicTacToe.sol` | Game contract |
| `migrations/` | Truffle deployment |
| `test/1_simple_test.js` | Join and a first move |
| `test/2_win_test.js` | A finished game |
| `app/` | Webpack client |
| `truffle-config.js` | Networks. Development uses Truffle’s built-in chain on port 9545 |

There is no `package.json` at the repository root. Install the client dependencies inside `app/`.

## Run

Use Truffle 5.x, which matches Solidity 0.5.12. Use a Node release that can still run Webpack 4 and `web3` 1.2 (Node 10 through 14).

```bash
cd app
npm install
cd ..
truffle compile
truffle test
truffle migrate
cd app
npm run dev
```

Open the dev-server URL in a browser whose MetaMask account is on the network you migrated to. Player 1 is the account that deployed the contract and must have sent 0.1 ether with that deployment.
