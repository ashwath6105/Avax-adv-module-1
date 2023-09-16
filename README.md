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

The ERC20 token and the Vault contract work together to handle deposits and withdrawals of the ERC20 token. A simple interface for interacting with the ERC20 token (CTX) and managing deposits and withdrawals is the Vault contract.


## Getting Started

### Executing program

To run this program, you can use Remix, an online Solidity IDE. To get started, go to the Remix website at https://remix.ethereum.org/.

Once you are on the Remix website, create a new file by clicking on the "+" icon in the left-hand sidebar. Save the file with a .sol extension (e.g., CREATRIX.sol). Copy and paste the following code into the file:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "CREATRIX";
    string public symbol = "CTX";
    uint8 public decimals = 18;

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    uint public playerLevel;
    mapping(address => uint[3]) public userFoundItems; // Store found items per user [0: Sword, 1: Shield, 2: BattleAxe]
    string public IG = "In-game items are: 1. Sword 2. Shield 3. Battle Axe";
    string public itemFound; // Store the name of the last item found

    constructor() {
        playerLevel = 1;
    }

    enum Item { Sword, Shield, BattleAxe }

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

    function findItem(uint itemNumber) external returns (string memory) {
        require(itemNumber >= 0 && itemNumber <= 2, "Invalid item number");

        Item item = Item(itemNumber);

        if (userFoundItems[msg.sender][itemNumber] < 1) {
            if (item == Item.Sword) {
                playerLevel += 1;
                userFoundItems[msg.sender][itemNumber] += 1;
                itemFound = "Sword";
            } else if (item == Item.Shield) {
                playerLevel += 2;
                userFoundItems[msg.sender][itemNumber] += 1;
                itemFound = "Shield";
            } else if (item == Item.BattleAxe) {
                playerLevel += 3;
                userFoundItems[msg.sender][itemNumber] += 1;
                itemFound = "BattleAxe";
            }
            return itemFound;
        }

        revert("You have already found this item");
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

The RPC URL for 'Mysubnet' is: http://127.0.0.1:9650/ext/bc/2gSDwydEFZBGzNXENypzqmN5324i9kEPSFM9FyuhxUKuAiVrz2/rpc

Token Symbol: CTX

Chain ID: 2611


Deploy these contracts to your Avalanche EVM subnet after setting it up.


To compile the code, click on the "Solidity Compiler" tab in the left-hand sidebar. Make sure the "Compiler" option is set to "0.8.17" (or another compatible version), and then click on the "Compile CREATRIX.sol" button.


After we compile the contract, we connect our  Metamask wallet to the Remix IDE.


Next, we change the environment to Injected Provider. 


Once the contract is deployed, you can interact with the various functions in the contract. 

By interacting with the finditem function, you can icrease the playerlevel of the charecter.

By interacting with the IG functions, you can see the items which the player can find in the game.

By interacting with the ItemFound function, you can see which item the player has found in the game.

## Authors

Ashwath R 

ashwathraju85@gmail.com

## License

This project is licensed under the MIT License - see the LICENSE.md file for details
