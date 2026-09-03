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
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TodoList {
    struct Todo {
        string text;
        bool completed;
    }

    mapping(address => Todo[]) public todos;

    event TodoAdded(address indexed user, uint256 index, string text);
    event TodoCompleted(address indexed user, uint256 index);

    function addTodo(string calldata text) external {
        todos[msg.sender].push(Todo(text, false));
        emit TodoAdded(msg.sender, todos[msg.sender].length - 1, text);
    }

    function completeTodo(uint256 index) external {
        require(index < todos[msg.sender].length, "Invalid index");
        todos[msg.sender][index].completed = true;
        emit TodoCompleted(msg.sender, index);
    }

    function getTodosCount() external view returns (uint256) {
        return todos[msg.sender].length;
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract KeyValue {
    mapping(string => string) private store;

    event ValueSet(string key, string value);

    function set(string calldata key, string calldata value) external {
        store[key] = value;
        emit ValueSet(key, value);
    }

    function get(string calldata key) external view returns (string memory) {
        return store[key];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ListManager {
    address[] public addresses;
    mapping(address => bool) public exists;

    event AddressAdded(address indexed account);
    event AddressRemoved(address indexed account);

    function addAddress(address account) external {
        require(account != address(0), "Invalid address");
        require(!exists[account], "Already exists");
        addresses.push(account);
        exists[account] = true;
        emit AddressAdded(account);
    }

    function getCount() external view returns (uint256) {
        return addresses.length;
    }

    function getAll() external view returns (address[] memory) {
        return addresses;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MinMax {
    uint256 public minValue = type(uint256).max;
    uint256 public maxValue;
    uint256 public totalSubmissions;

    event ValueSubmitted(address indexed user, uint256 value);

    function submit(uint256 value) external {
        if (value < minValue) {
            minValue = value;
        }
        if (value > maxValue) {
            maxValue = value;
        }
        totalSubmissions += 1;
        emit ValueSubmitted(msg.sender, value);
    }

    function getMinMax() external view returns (uint256, uint256) {
        return (minValue, maxValue);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract DailyCounter {
    mapping(address => uint256) public lastDay;
    mapping(address => uint256) public dailyCount;

    event Counted(address indexed user, uint256 count);

    function count() external {
        uint256 today = block.timestamp / 1 days;

        if (lastDay[msg.sender] != today) {
            lastDay[msg.sender] = today;
            dailyCount[msg.sender] = 0;
        }

        dailyCount[msg.sender] += 1;
        emit Counted(msg.sender, dailyCount[msg.sender]);
    }

    function getDailyCount(address user) external view returns (uint256) {
        uint256 today = block.timestamp / 1 days;
        if (lastDay[user] != today) return 0;
        return dailyCount[user];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleEscrow {
    address public payer;
    address public payee;
    uint256 public amount;
    bool public funded;
    bool public released;

    event Funded(address indexed from, uint256 amount);
    event Released(address indexed to, uint256 amount);

    constructor(address _payee) {
        payer = msg.sender;
        payee = _payee;
    }

    function fund() external payable {
        require(msg.sender == payer, "Only payer");
        require(!funded, "Already funded");
        require(msg.value > 0, "Must send ETH");
        amount = msg.value;
        funded = true;
        emit Funded(msg.sender, msg.value);
    }

    function release() external {
        require(msg.sender == payer, "Only payer");
        require(funded && !released, "Cannot release");
        released = true;
        (bool success, ) = payee.call{value: amount}("");
        require(success, "Transfer failed");
        emit Released(payee, amount);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleRegistry {
    mapping(string => address) public registry;

    event Registered(string name, address indexed account);
    event Unregistered(string name);

    function register(string calldata name) external {
        require(registry[name] == address(0), "Name already taken");
        registry[name] = msg.sender;
        emit Registered(name, msg.sender);
    }

    function unregister(string calldata name) external {
        require(registry[name] == msg.sender, "Not owner of name");
        delete registry[name];
        emit Unregistered(name);
    }

    function resolve(string calldata name) external view returns (address) {
        return registry[name];
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract HashStore {
    mapping(address => bytes32) public hashes;

    event HashStored(address indexed user, bytes32 hash);

    function storeHash(bytes32 hash) external {
        hashes[msg.sender] = hash;
        emit HashStored(msg.sender, hash);
    }

    function getHash(address user) external view returns (bytes32) {
        return hashes[user];
    }

    function verifyHash(address user, bytes32 hash) external view returns (bool) {
        return hashes[user] == hash;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract IdGenerator {
    uint256 public nextId = 1;
    mapping(address => uint256) public userIds;

    event IdAssigned(address indexed user, uint256 id);

    function generateId() external returns (uint256) {
        require(userIds[msg.sender] == 0, "Already has ID");
        uint256 id = nextId;
        nextId += 1;
        userIds[msg.sender] = id;
        emit IdAssigned(msg.sender, id);
        return id;
    }

    function getId(address user) external view returns (uint256) {
        return userIds[user];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BalanceSnapshot {
    mapping(address => uint256) public snapshots;
    mapping(address => uint256) public snapshotTime;

    event SnapshotTaken(address indexed user, uint256 balance, uint256 timestamp);

    function takeSnapshot() external {
        snapshots[msg.sender] = msg.sender.balance;
        snapshotTime[msg.sender] = block.timestamp;
        emit SnapshotTaken(msg.sender, msg.sender.balance, block.timestamp);
    }

    function getSnapshot(address user) external view returns (uint256 balance, uint256 timestamp) {
        return (snapshots[user], snapshotTime[user]);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Version {
    string public version = "1.0.0";
    address public updater;
    uint256 public updatedAt;

    event VersionUpdated(string newVersion, address indexed by);

    function updateVersion(string calldata newVersion) external {
        version = newVersion;
        updater = msg.sender;
        updatedAt = block.timestamp;
        emit VersionUpdated(newVersion, msg.sender);
    }

    function getVersionInfo() external view returns (string memory, address, uint256) {
        return (version, updater, updatedAt);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract EventLogger {
    event Log(address indexed sender, string message, uint256 timestamp);
    event NumberLog(address indexed sender, uint256 number, uint256 timestamp);

    function logMessage(string calldata message) external {
        emit Log(msg.sender, message, block.timestamp);
    }

    function logNumber(uint256 number) external {
        emit NumberLog(msg.sender, number, block.timestamp);
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract StringStore {
    string public data;
    address public lastEditor;
    uint256 public editCount;

    event DataUpdated(string newData, address indexed editor);

    function setData(string calldata newData) external {
        data = newData;
        lastEditor = msg.sender;
        editCount += 1;
        emit DataUpdated(newData, msg.sender);
    }

    function getInfo() external view returns (string memory, address, uint256) {
        return (data, lastEditor, editCount);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Config {
    address public owner;
    uint256 public maxSupply;
    uint256 public feePercent;
    bool public publicMint;

    event ConfigUpdated(uint256 maxSupply, uint256 feePercent, bool publicMint);

    constructor() {
        owner = msg.sender;
        maxSupply = 10000;
        feePercent = 5;
        publicMint = false;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    function updateConfig(uint256 _maxSupply, uint256 _feePercent, bool _publicMint) external onlyOwner {
        maxSupply = _maxSupply;
        feePercent = _feePercent;
        publicMint = _publicMint;
        emit ConfigUpdated(_maxSupply, _feePercent, _publicMint);
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract DataFeed {
    int256 public value;
    uint256 public updatedAt;
    address public updater;

    event ValueUpdated(int256 newValue, uint256 timestamp);

    function updateValue(int256 newValue) external {
        value = newValue;
        updatedAt = block.timestamp;
        updater = msg.sender;
        emit ValueUpdated(newValue, block.timestamp);
    }

    function getData() external view returns (int256, uint256, address) {
        return (value, updatedAt, updater);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BatchIncrement {
    mapping(address => uint256) public counts;

    event Incremented(address indexed user, uint256 times, uint256 newTotal);

    function increment(uint256 times) external {
        require(times > 0 && times <= 100, "Invalid times");
        counts[msg.sender] += times;
        emit Incremented(msg.sender, times, counts[msg.sender]);
    }

    function getCount(address user) external view returns (uint256) {
        return counts[user];
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TextBoard {
    string[] public messages;
    address[] public authors;

    event MessagePosted(address indexed author, string message, uint256 index);

    function post(string calldata message) external {
        require(bytes(message).length > 0, "Empty message");
        messages.push(message);
        authors.push(msg.sender);
        emit MessagePosted(msg.sender, message, messages.length - 1);
    }

    function getMessage(uint256 index) external view returns (string memory, address) {
        require(index < messages.length, "Invalid index");
        return (messages[index], authors[index]);
    }

    function getMessageCount() external view returns (uint256) {
        return messages.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Waitlist {
    address[] public list;
    mapping(address => bool) public isListed;
    uint256 public maxSize;

    event Joined(address indexed user, uint256 position);

    constructor(uint256 _maxSize) {
        maxSize = _maxSize;
    }

    function join() external {
        require(!isListed[msg.sender], "Already in list");
        require(list.length < maxSize, "Waitlist full");
        list.push(msg.sender);
        isListed[msg.sender] = true;
        emit Joined(msg.sender, list.length - 1);
    }

    function getPosition(address user) external view returns (uint256) {
        require(isListed[user], "Not in list");
        for (uint256 i = 0; i < list.length; i++) {
            if (list[i] == user) return i;
        }
        revert("Not found");
    }

    function getLength() external view returns (uint256) {
        return list.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract PriceTag {
    mapping(string => uint256) public prices;
    address public owner;

    event PriceSet(string item, uint256 price);

    constructor() {
        owner = msg.sender;
    }

    function setPrice(string calldata item, uint256 price) external {
        require(msg.sender == owner, "Not owner");
        prices[item] = price;
        emit PriceSet(item, price);
    }

    function getPrice(string calldata item) external view returns (uint256) {
        return prices[item];
    }

    function removePrice(string calldata item) external {
        require(msg.sender == owner, "Not owner");
        delete prices[item];
        emit PriceSet(item, 0);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleOracle {
    address public owner;
    int256 public price;
    uint256 public lastUpdate;

    event PriceUpdated(int256 newPrice, uint256 timestamp);

    constructor() {
        owner = msg.sender;
    }

    function updatePrice(int256 newPrice) external {
        require(msg.sender == owner, "Not owner");
        price = newPrice;
        lastUpdate = block.timestamp;
        emit PriceUpdated(newPrice, block.timestamp);
    }

    function getPrice() external view returns (int256, uint256) {
        return (price, lastUpdate);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TimestampLog {
    uint256[] public timestamps;
    address[] public loggers;

    event Logged(address indexed user, uint256 timestamp, uint256 index);

    function log() external {
        timestamps.push(block.timestamp);
        loggers.push(msg.sender);
        emit Logged(msg.sender, block.timestamp, timestamps.length - 1);
    }

    function getLog(uint256 index) external view returns (uint256, address) {
        require(index < timestamps.length, "Invalid index");
        return (timestamps[index], loggers[index]);
    }

    function getCount() external view returns (uint256) {
        return timestamps.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MessageQueue {
    string[] private queue;
    uint256 public head;

    event MessageEnqueued(string message, uint256 index);
    event MessageDequeued(string message);

    function enqueue(string calldata message) external {
        queue.push(message);
        emit MessageEnqueued(message, queue.length - 1);
    }

    function dequeue() external returns (string memory) {
        require(head < queue.length, "Queue empty");
        string memory message = queue[head];
        head += 1;
        emit MessageDequeued(message);
        return message;
    }

    function length() external view returns (uint256) {
        return queue.length - head;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleTimer {
    uint256 public startTime;
    uint256 public endTime;
    address public starter;

    event TimerStarted(address indexed by, uint256 start, uint256 end);
    event TimerReset();

    function start(uint256 durationInSeconds) external {
        require(startTime == 0, "Timer already started");
        startTime = block.timestamp;
        endTime = block.timestamp + durationInSeconds;
        starter = msg.sender;
        emit TimerStarted(msg.sender, startTime, endTime);
    }

    function isFinished() external view returns (bool) {
        return block.timestamp >= endTime && endTime != 0;
    }

    function reset() external {
        require(msg.sender == starter, "Not starter");
        startTime = 0;
        endTime = 0;
        emit TimerReset();
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ValueHistory {
    uint256[] public values;
    uint256[] public timestamps;

    event ValueRecorded(uint256 value, uint256 timestamp, uint256 index);

    function record(uint256 value) external {
        values.push(value);
        timestamps.push(block.timestamp);
        emit ValueRecorded(value, block.timestamp, values.length - 1);
    }

    function getRecord(uint256 index) external view returns (uint256 value, uint256 timestamp) {
        require(index < values.length, "Invalid index");
        return (values[index], timestamps[index]);
    }

    function getCount() external view returns (uint256) {
        return values.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract AddressBook {
    mapping(address => mapping(string => address)) public contacts;

    event ContactAdded(address indexed owner, string name, address contact);
    event ContactRemoved(address indexed owner, string name);

    function addContact(string calldata name, address contact) external {
        require(contact != address(0), "Invalid address");
        contacts[msg.sender][name] = contact;
        emit ContactAdded(msg.sender, name, contact);
    }

    function removeContact(string calldata name) external {
        delete contacts[msg.sender][name];
        emit ContactRemoved(msg.sender, name);
    }

    function getContact(address owner, string calldata name) external view returns (address) {
        return contacts[owner][name];
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract NoteHistory {
    struct Note {
        string text;
        uint256 timestamp;
    }

    mapping(address => Note[]) public notes;

    event NoteAdded(address indexed user, string text, uint256 timestamp);

    function addNote(string calldata text) external {
        notes[msg.sender].push(Note(text, block.timestamp));
        emit NoteAdded(msg.sender, text, block.timestamp);
    }

    function getNotesCount(address user) external view returns (uint256) {
        return notes[user].length;
    }

    function getNote(address user, uint256 index) external view returns (string memory, uint256) {
        require(index < notes[user].length, "Invalid index");
        Note memory n = notes[user][index];
        return (n.text, n.timestamp);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract CounterHistory {
    uint256 public count;
    uint256[] public history;

    event Incremented(uint256 newCount);

    function increment() external {
        count += 1;
        history.push(count);
        emit Incremented(count);
    }

    function getHistoryLength() external view returns (uint256) {
        return history.length;
    }

    function getHistoryAt(uint256 index) external view returns (uint256) {
        require(index < history.length, "Invalid index");
        return history[index];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract IndexStore {
    mapping(uint256 => string) public data;
    uint256 public nextIndex;

    event Stored(uint256 indexed index, string value);

    function store(string calldata value) external returns (uint256) {
        uint256 index = nextIndex;
        data[index] = value;
        nextIndex += 1;
        emit Stored(index, value);
        return index;
    }

    function get(uint256 index) external view returns (string memory) {
        return data[index];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Progress {
    mapping(address => uint256) public progress;
    uint256 public maxProgress = 100;

    event ProgressUpdated(address indexed user, uint256 value);

    function setProgress(uint256 value) external {
        require(value <= maxProgress, "Exceeds max");
        progress[msg.sender] = value;
        emit ProgressUpdated(msg.sender, value);
    }

    function addProgress(uint256 amount) external {
        uint256 newValue = progress[msg.sender] + amount;
        require(newValue <= maxProgress, "Exceeds max");
        progress[msg.sender] = newValue;
        emit ProgressUpdated(msg.sender, newValue);
    }

    function getProgress(address user) external view returns (uint256) {
        return progress[user];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract UserCounter {
    mapping(address => uint256) public counts;
    uint256 public globalCount;

    event Counted(address indexed user, uint256 userCount, uint256 globalCount);

    function count() external {
        counts[msg.sender] += 1;
        globalCount += 1;
        emit Counted(msg.sender, counts[msg.sender], globalCount);
    }

    function getCount(address user) external view returns (uint256) {
        return counts[user];
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract EventCounter {
    uint256 public totalEvents;
    mapping(address => uint256) public userEvents;

    event EventEmitted(address indexed user, uint256 userTotal, uint256 globalTotal);

    function emitEvent() external {
        totalEvents += 1;
        userEvents[msg.sender] += 1;
        emit EventEmitted(msg.sender, userEvents[msg.sender], totalEvents);
    }

    function getStats(address user) external view returns (uint256 userTotal, uint256 globalTotal) {
        return (userEvents[user], totalEvents);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract IndexCounter {
    uint256 public currentIndex;
    mapping(uint256 => address) public indexOwner;

    event IndexClaimed(uint256 indexed index, address indexed user);

    function claimIndex() external returns (uint256) {
        uint256 index = currentIndex;
        currentIndex += 1;
        indexOwner[index] = msg.sender;
        emit IndexClaimed(index, msg.sender);
        return index;
    }

    function getOwner(uint256 index) external view returns (address) {
        return indexOwner[index];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract StepLog {
    mapping(address => uint256[]) public steps;

    event StepsLogged(address indexed user, uint256 amount, uint256 timestamp);

    function logSteps(uint256 amount) external {
        require(amount > 0, "Amount must be > 0");
        steps[msg.sender].push(amount);
        emit StepsLogged(msg.sender, amount, block.timestamp);
    }

    function getStepsCount(address user) external view returns (uint256) {
        return steps[user].length;
    }

    function getStepAt(address user, uint256 index) external view returns (uint256) {
        require(index < steps[user].length, "Invalid index");
        r// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract NumberList {
    uint256[] public numbers;

    event NumberAdded(uint256 number, uint256 index);

    function add(uint256 number) external {
        numbers.push(number);
        emit NumberAdded(number, numbers.length - 1);
    }

    function get(uint256 index) external view returns (uint256) {
        require(index < numbers.length, "Index out of bounds");
        return numbers[index];
    }

    function length() external view returns (uint256) {
        return numbers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract StringList {
    string[] public items;

    event ItemAdded(string item, uint256 index);

    function add(string calldata item) external {
        items.push(item);
        emit ItemAdded(item, items.length - 1);
    }

    function get(uint256 index) external view returns (string memory) {
        require(index < items.length, "Index out of bounds");
        return items[index];
    }

    function length() external view returns (uint256) {
        return items.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Record {
    struct Entry {
        string data;
        uint256 timestamp;
        address author;
    }

    Entry[] public entries;

    event Recorded(uint256 indexed index, address indexed author, string data);

    function record(string calldata data) external {
        entries.push(Entry(data, block.timestamp, msg.sender));
        emit Recorded(entries.length - 1, msg.sender, data);
    }

    function getEntry(uint256 index) external view returns (string memory, uint256, address) {
        require(index < entries.length, "Invalid index");
        Entry memory e = entries[index];
        return (e.data, e.timestamp, e.author);
    }

    function getCount() external view returns (uint256) {
        return entries.length;
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Logbook {
    string[] public logs;
    address[] public authors;
    uint256[] public timestamps;

    event Logged(address indexed author, string message, uint256 index);

    function write(string calldata message) external {
        logs.push(message);
        authors.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Logged(msg.sender, message, logs.length - 1);
    }

    function getLog(uint256 index) external view returns (string memory, address, uint256) {
        require(index < logs.length, "Invalid index");
        return (logs[index], authors[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return logs.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Trace {
    address[] public tracers;
    uint256[] public times;

    event Traced(address indexed user, uint256 timestamp, uint256 index);

    function trace() external {
        tracers.push(msg.sender);
        times.push(block.timestamp);
        emit Traced(msg.sender, block.timestamp, tracers.length - 1);
    }

    function getTrace(uint256 index) external view returns (address, uint256) {
        require(index < tracers.length, "Invalid index");
        return (tracers[index], times[index]);
    }

    function count() external view returns (uint256) {
        return tracers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract NoteLog {
    struct Note {
        address author;
        string text;
        uint256 timestamp;
    }

    Note[] public notes;

    event NoteAdded(address indexed author, string text, uint256 index);

    function addNote(string calldata text) external {
        notes.push(Note(msg.sender, text, block.timestamp));
        emit NoteAdded(msg.sender, text, notes.length - 1);
    }

    function getNote(uint256 index) external view returns (address, string memory, uint256) {
        require(index < notes.length, "Invalid index");
        Note memory n = notes[index];
        return (n.author, n.text, n.timestamp);
    }

    function getCount() external view returns (uint256) {
        return notes.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Stamp {
    address[] public stampers;
    uint256[] public times;

    event Stamped(address indexed user, uint256 timestamp, uint256 index);

    function stamp() external {
        stampers.push(msg.sender);
        times.push(block.timestamp);
        emit Stamped(msg.sender, block.timestamp, stampers.length - 1);
    }

    function getStamp(uint256 index) external view returns (address, uint256) {
        require(index < stampers.length, "Invalid index");
        return (stampers[index], times[index]);
    }

    function count() external view returns (uint256) {
        return stampers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Beacon {
    address[] public beacons;
    uint256[] public timestamps;

    event BeaconLit(address indexed user, uint256 timestamp, uint256 index);

    function light() external {
        beacons.push(msg.sender);
        timestamps.push(block.timestamp);
        emit BeaconLit(msg.sender, block.timestamp, beacons.length - 1);
    }

    function getBeacon(uint256 index) external view returns (address, uint256) {
        require(index < beacons.length, "Invalid index");
        return (beacons[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return beacons.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Flash {
    address[] public flashers;
    uint256[] public timestamps;

    event Flashed(address indexed user, uint256 timestamp, uint256 index);

    function flash() external {
        flashers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Flashed(msg.sender, block.timestamp, flashers.length - 1);
    }

    function getFlash(uint256 index) external view returns (address, uint256) {
        require(index < flashers.length, "Invalid index");
        return (flashers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return flashers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract EchoLog {
    address[] public users;
    uint256[] public times;

    event Echoed(address indexed user, uint256 timestamp, uint256 index);

    function echo() external {
        users.push(msg.sender);
        times.push(block.timestamp);
        emit Echoed(msg.sender, block.timestamp, users.length - 1);
    }

    function getEcho(uint256 index) external view returns (address, uint256) {
        require(index < users.length, "Invalid index");
        return (users[index], times[index]);
    }

    function count() external view returns (uint256) {
        return users.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ClickLog {
    address[] public clickers;
    uint256[] public timestamps;

    event Clicked(address indexed user, uint256 timestamp, uint256 index);

    function click() external {
        clickers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Clicked(msg.sender, block.timestamp, clickers.length - 1);
    }

    function getClick(uint256 index) external view returns (address, uint256) {
        require(index < clickers.length, "Invalid index");
        return (clickers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return clickers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TapLog {
    address[] public tappers;
    uint256[] public timestamps;

    event Tapped(address indexed user, uint256 timestamp, uint256 index);

    function tap() external {
        tappers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Tapped(msg.sender, block.timestamp, tappers.length - 1);
    }

    function getTap(uint256 index) external view returns (address, uint256) {
        require(index < tappers.length, "Invalid index");
        return (tappers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return tappers.length;
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract HitLog {
    address[] public hitters;
    uint256[] public timestamps;

    event Hit(address indexed user, uint256 timestamp, uint256 index);

    function hit() external {
        hitters.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Hit(msg.sender, block.timestamp, hitters.length - 1);
    }

    function getHit(uint256 index) external view returns (address, uint256) {
        require(index < hitters.length, "Invalid index");
        return (hitters[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return hitters.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TouchLog {
    address[] public touchers;
    uint256[] public timestamps;

    event Touched(address indexed user, uint256 timestamp, uint256 index);

    function touch() external {
        touchers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Touched(msg.sender, block.timestamp, touchers.length - 1);
    }

    function getTouch(uint256 index) external view returns (address, uint256) {
        require(index < touchers.length, "Invalid index");
        return (touchers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return touchers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MarkLog {
    address[] public markers;
    uint256[] public timestamps;

    event Marked(address indexed user, uint256 timestamp, uint256 index);

    function mark() external {
        markers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Marked(msg.sender, block.timestamp, markers.length - 1);
    }

    function getMark(uint256 index) external view returns (address, uint256) {
        require(index < markers.length, "Invalid index");
        return (markers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return markers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract StampLog {
    address[] public stampers;
    uint256[] public timestamps;

    event Stamped(address indexed user, uint256 timestamp, uint256 index);

    function stamp() external {
        stampers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Stamped(msg.sender, block.timestamp, stampers.length - 1);
    }

    function getStamp(uint256 index) external view returns (address, uint256) {
        require(index < stampers.length, "Invalid index");
        return (stampers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return stampers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SignalLog {
    address[] public signalers;
    uint256[] public timestamps;

    event Signaled(address indexed user, uint256 timestamp, uint256 index);

    function signal() external {
        signalers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Signaled(msg.sender, block.timestamp, signalers.length - 1);
    }

    function getSignal(uint256 index) external view returns (address, uint256) {
        require(index < signalers.length, "Invalid index");
        return (signalers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return signalers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TraceLog {
    address[] public tracers;
    uint256[] public timestamps;

    event Traced(address indexed user, uint256 timestamp, uint256 index);

    function trace() external {
        tracers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Traced(msg.sender, block.timestamp, tracers.length - 1);
    }

    function getTrace(uint256 index) external view returns (address, uint256) {
        require(index < tracers.length, "Invalid index");
        return (tracers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return tracers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract PingLog {
    address[] public pingers;
    uint256[] public timestamps;

    event Pinged(address indexed user, uint256 timestamp, uint256 index);

    function ping() external {
        pingers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Pinged(msg.sender, block.timestamp, pingers.length - 1);
    }

    function getPing(uint256 index) external view returns (address, uint256) {
        require(index < pingers.length, "Invalid index");
        return (pingers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return pingers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ClickTrace {
    address[] public clickers;
    uint256[] public timestamps;

    event Clicked(address indexed user, uint256 timestamp, uint256 index);

    function click() external {
        clickers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Clicked(msg.sender, block.timestamp, clickers.length - 1);
    }

    function getClick(uint256 index) external view returns (address, uint256) {
        require(index < clickers.length, "Invalid index");
        return (clickers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return clickers.length;
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TapTrace {
    address[] public tappers;
    uint256[] public timestamps;

    event Tapped(address indexed user, uint256 timestamp, uint256 index);

    function tap() external {
        tappers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Tapped(msg.sender, block.timestamp, tappers.length - 1);
    }

    function getTap(uint256 index) external view returns (address, uint256) {
        require(index < tappers.length, "Invalid index");
        return (tappers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return tappers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MarkTrace {
    address[] public markers;
    uint256[] public timestamps;

    event Marked(address indexed user, uint256 timestamp, uint256 index);

    function mark() external {
        markers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Marked(msg.sender, block.timestamp, markers.length - 1);
    }

    function getMark(uint256 index) external view returns (address, uint256) {
        require(index < markers.length, "Invalid index");
        return (markers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return markers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SignalTrace {
    address[] public signalers;
    uint256[] public timestamps;

    event Signaled(address indexed user, uint256 timestamp, uint256 index);

    function signal() external {
        signalers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Signaled(msg.sender, block.timestamp, signalers.length - 1);
    }

    function getSignal(uint256 index) external view returns (address, uint256) {
        require(index < signalers.length, "Invalid index");
        return (signalers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return signalers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TraceMark {
    address[] public tracers;
    uint256[] public timestamps;

    event Traced(address indexed user, uint256 timestamp, uint256 index);

    function trace() external {
        tracers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Traced(msg.sender, block.timestamp, tracers.length - 1);
    }

    function getTrace(uint256 index) external view returns (address, uint256) {
        require(index < tracers.length, "Invalid index");
        return (tracers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return tracers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract PingTrace {
    address[] public pingers;
    uint256[] public timestamps;

    event Pinged(address indexed user, uint256 timestamp, uint256 index);

    function ping() external {
        pingers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Pinged(msg.sender, block.timestamp, pingers.length - 1);
    }

    function getPing(uint256 index) external view returns (address, uint256) {
        require(index < pingers.length, "Invalid index");
        return (pingers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return pingers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ClickMark {
    address[] public clickers;
    uint256[] public timestamps;

    event Clicked(address indexed user, uint256 timestamp, uint256 index);

    function click() external {
        clickers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Clicked(msg.sender, block.timestamp, clickers.length - 1);
    }

    function getClick(uint256 index) external view returns (address, uint256) {
        require(index < clickers.length, "Invalid index");
        return (clickers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return clickers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TapMark {
    address[] public tappers;
    uint256[] public timestamps;

    event Tapped(address indexed user, uint256 timestamp, uint256 index);

    function tap() external {
        tappers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Tapped(msg.sender, block.timestamp, tappers.length - 1);
    }

    function getTap(uint256 index) external view returns (address, uint256) {
        require(index < tappers.length, "Invalid index");
        return (tappers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return tappers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract HitMark {
    address[] public hitters;
    uint256[] public timestamps;

    event Hit(address indexed user, uint256 timestamp, uint256 index);

    function hit() external {
        hitters.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Hit(msg.sender, block.timestamp, hitters.length - 1);
    }

    function getHit(uint256 index) external view returns (address, uint256) {
        require(index < hitters.length, "Invalid index");
        return (hitters[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return hitters.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MarkStamp {
    address[] public markers;
    uint256[] public timestamps;

    event Marked(address indexed user, uint256 timestamp, uint256 index);

    function mark() external {
        markers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Marked(msg.sender, block.timestamp, markers.length - 1);
    }

    function getMark(uint256 index) external view returns (address, uint256) {
        require(index < markers.length, "Invalid index");
        return (markers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return markers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SignalMark {
    address[] public signalers;
    uint256[] public timestamps;

    event Signaled(address indexed user, uint256 timestamp, uint256 index);

    function signal() external {
        signalers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Signaled(msg.sender, block.timestamp, signalers.length - 1);
    }

    function getSignal(uint256 index) external view returns (address, uint256) {
        require(index < signalers.length, "Invalid index");
        return (signalers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return signalers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TraceStamp {
    address[] public tracers;
    uint256[] public timestamps;

    event Traced(address indexed user, uint256 timestamp, uint256 index);

    function trace() external {
        tracers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Traced(msg.sender, block.timestamp, tracers.length - 1);
    }

    function getTrace(uint256 index) external view returns (address, uint256) {
        require(index < tracers.length, "Invalid index");
        return (tracers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return tracers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract PingStamp {
    address[] public pingers;
    uint256[] public timestamps;

    event Pinged(address indexed user, uint256 timestamp, uint256 index);

    function ping() external {
        pingers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Pinged(msg.sender, block.timestamp, pingers.length - 1);
    }

    function getPing(uint256 index) external view returns (address, uint256) {
        require(index < pingers.length, "Invalid index");
        return (pingers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return pingers.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ClickStamp {
    address[] public clickers;
    uint256[] public timestamps;

    event Clicked(address indexed user, uint256 timestamp, uint256 index);

    function click() external {
        clickers.push(msg.sender);
        timestamps.push(block.timestamp);
        emit Clicked(msg.sender, block.timestamp, clickers.length - 1);
    }

    function getClick(uint256 index) external view returns (address, uint256) {
        require(index < clickers.length, "Invalid index");
        return (clickers[index], timestamps[index]);
    }

    function count() external view returns (uint256) {
        return clickers.length;
    }
}
