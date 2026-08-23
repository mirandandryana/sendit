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
