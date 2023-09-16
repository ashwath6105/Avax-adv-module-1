# Avax Intermediate - DeFi Kingdom

This is a solidity smart contract that implements a basic explore-and-level-up concept in a DeFi kingdom game. This code has a function in which a player levels up as he finds one of three given items, i.e, "Sword", "Shield" and "BattleAxe".

## Description
This contract has a function called "finditem" in which the level of a player increase by a certain number when he finds a specific given item in a game.

The given items are:
1. Sword
   
2. Sheild
   
3. BattleAxe

The player's level also increases by a certain number as he finds these items.

The player's level increases by:

+1 if he finds a "Sword"

+2 if he finds a "Sheild"

+3 if he finds a "BattleAxe"

This program implements a standard ERC20 token, named as CREATRIX, CTX.

It also has functions to transfer tokens, approve token allowances, and manage the token's total supply, individual balances, mint and burn tokens for creation and burning of tokens.

Vault Contract:

The Vault contract interacts with an ERC20 token and acts as a mechanism to handle deposits and withdrawals of the ERC20 token. The Vault contract is a basic interface which interacts with the ERC20 token (CTX) and handles deposits and withdrawals of the ERC20(CTX) token.


## Getting Started

### Executing program

To run this program, you can use Remix, an online Solidity IDE. To get started, go to the Remix website at https://remix.ethereum.org/.

Once you are on the Remix website, create a new file by clicking on the "+" icon in the left-hand sidebar. Save the file with a .sol extension (e.g., ADROCX.sol). Copy and paste the following code into the file:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "ADROCX";
    string public symbol = "ADX";
    uint8 public decimals = 18;

		event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
    address public target;
    uint public lives;
    constructor()
    {
        target = msg.sender;
        lives = 7;
    }
    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
    function aim_and_shoot() public returns(uint)
    {
      assert(target == msg.sender);  
      lives = lives - 1;
      return lives;
    }
}


interface IERC20 {
    function totalSupply() external view returns (uint);

    function balanceOf(address account) external view returns (uint);

    function transfer(address recipient, uint amount) external returns (bool);

    function allowance(address owner, address spender) external view returns (uint);

    function approve(address spender, uint amount) external returns (bool);

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}

contract Vault {
    IERC20 public immutable token;

    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }

    function _burn(address _from, uint _shares) private {
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }

    function deposit(uint _amount) external {
        /*
        a = amount
        B = balance of token before deposit
        T = total supply
        s = shares to mint

        (T + s) / T = (a + B) / B 

        s = aT / B
        */
        uint shares;
        if (totalSupply == 0) {
            shares = _amount;
        } else {
            shares = (_amount * totalSupply) / token.balanceOf(address(this));
        }

        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint _shares) external {
        /*
        a = amount
        B = balance of token before withdraw
        T = total supply
        s = shares to burn

        (T - s) / T = (B - a) / B 

        a = sB / T
        */
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        token.transfer(msg.sender, amount);
    }
}

```
To set-up the Avalanche EVM subnet, we need RPC URL, token symbol and Chain ID.

The RPC URL for 'mySubnet' is: http://127.0.0.1:9650/ext/bc/21eAJZ7zRrCzxbQQxSLDDvYSFZL4v6QJMEBh5RWiv2FBQR7U1j/rpc

Token Symbol: ADX

Chain ID: 2965


Deploy these contracts to your Avalanche EVM subnet after setting it up.


To compile the code, click on the "Solidity Compiler" tab in the left-hand sidebar. Make sure the "Compiler" option is set to "0.8.17" (or another compatible version), and then click on the "Compile ADROCX.sol" button.


After we compile the contract, we connect our  Metamask wallet to the Remix IDE.


Next, we change the environment to Injected Provider. 


Once the contract is deployed, you can interact with it to mint, burn, transfer tokens. 


Upon interacting with the aim_and_shoot function, you will find that the 'lives' is decremented by 1.



## Authors

Aditya Raju  

adrocxsmma@gmail.com

## License

This project is licensed under the MIT License - see the LICENSE.md file for details
