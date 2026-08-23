# Base Guild Learn

Repositorio educativo del Guild de Base.

Aquí encontrarás material para aprender desde cero cómo desarrollar y desplegar en la red Base.

Base permite a cualquier developer de Ethereum desplegar contratos casi sin cambios, pero con costos mucho más bajos.

# Ecosistema Base

Base ha crecido gracias a:
- Integración nativa con Coinbase
- Gran cantidad de protocolos DeFi
- Buena infraestructura de bridges y oráculos
- Comunidades activas de builders

Es un entorno excelente para construir y experimentar.

# Stack recomendado

- Solidity
- Foundry o Hardhat
- Base Mainnet (8453) o Base Sepolia
- BaseScan para verificar contratos

Construyamos en público.

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract OwnableCounter {
    uint256 public count;
    address public owner;

    event CountChanged(uint256 newCount);
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    function increment() external onlyOwner {
        count += 1;
        emit CountChanged(count);
    }

    function reset() external onlyOwner {
        count = 0;
        emit CountChanged(count);
    }

    function transferOwnership(address newOwner) external onlyOwner {
        require(newOwner != address(0), "Invalid address");
        emit OwnershipTransferred(owner, newOwner);
        owner = newOwner;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MessageBoard {
    string public lastMessage;
    address public lastAuthor;
    uint256 public messageCount;

    event NewMessage(address indexed author, string message);

    function postMessage(string calldata message) external {
        lastMessage = message;
        lastAuthor = msg.sender;
        messageCount += 1;
        emit NewMessage(msg.sender, message);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Vote {
    uint256 public yesVotes;
    uint256 public noVotes;
    mapping(address => bool) public hasVoted;

    event Voted(address indexed voter, bool support);

    function vote(bool support) external {
        require(!hasVoted[msg.sender], "Already voted");
        hasVoted[msg.sender] = true;

        if (support) {
            yesVotes += 1;
        } else {
            noVotes += 1;
        }

        emit Voted(msg.sender, support);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Ownership {
    address public owner;

    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

    constructor() {
        owner = msg.sender;
        emit OwnershipTransferred(address(0), msg.sender);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    function transferOwnership(address newOwner) external onlyOwner {
        require(newOwner != address(0), "Invalid address");
        emit OwnershipTransferred(owner, newOwner);
        owner = newOwner;
    }

    function renounceOwnership() external onlyOwner {
        emit OwnershipTransferred(owner, address(0));
        owner = address(0);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract StructStore {
    struct User {
        string name;
        uint256 age;
        bool registered;
    }

    mapping(address => User) public users;

    event UserRegistered(address indexed user, string name, uint256 age);

    function register(string calldata name, uint256 age) external {
        require(!users[msg.sender].registered, "Already registered");
        users[msg.sender] = User(name, age, true);
        emit UserRegistered(msg.sender, name, age);
    }

    function getUser(address user) external view returns (string memory, uint256, bool) {
        User memory u = users[user];
        return (u.name, u.age, u.registered);
    }
}
