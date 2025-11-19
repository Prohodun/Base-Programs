# 🎯Base Learn Newcomer
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BasicMath {

    function adder(uint _a, uint _b) public pure returns (uint sum, bool error) {
        // Use `unchecked` to allow overflow
        unchecked {
            uint c = _a + _b;
            // If the result of the addition is smaller than _a, it means an overflow has occurred
            if (c < _a) {
                return (0, true);
            }
            return (c, false);
        }
    }

    function subtractor(uint _a, uint _b) public pure returns (uint difference, bool error) {
        // Manually check whether underflow will occur (when _a is smaller than _b)
        if (_a < _b) {
            return (0, true);
        }
        
        // Use `unchecked` to avoid Solidity's underflow check which will stop the transaction
        unchecked {
            uint c = _a - _b;
            return (c, false);
        }
    }
}
```

- Compile yourfile.sol
- Click deploy & Run tx
- Select environment
- Borwser extension - Inject Coinbase wallet
- Switch on wallet (Base Sepolia Testnet)
- Deploy & Approve
- Copy contract address
- Paste contract : https://docs.base.org/learn/deployment-to-testnet/deployment-to-testnet-exercise
- Approve tx on wallet
- Verify on Guild base

# 🎯Base Learn Acolyte

## 🔵Control Structures Pin NFT
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

// Custom error definition for the doNotDisturb function
error AfterHours(uint timeProvided);

contract ControlStructures {

    function fizzBuzz(uint _number) public pure returns (string memory) {
        // Check the most specific conditions first: divisible by 3 and 5
        if (_number % 3 == 0 && _number % 5 == 0) {
            return "FizzBuzz";
        } 
        // Check the condition of being divisible by 3
        else if (_number % 3 == 0) {
            return "Fizz";
        } 
        // Check the condition of being divisible by 5
        else if (_number % 5 == 0) {
            return "Buzz";
        } 
        // If none of the above conditions are met
        else {
            return "Splat";
        }
    }

    function doNotDisturb(uint _time) public pure returns (string memory) {
        // If _time >= 2400, trigger panic (internal error)
        if (_time >= 2400) {
            assert(_time < 2400); 
        }

        // If _time > 2200 or < 800, revert with custom error
        if (_time > 2200 || _time < 800) {
            revert AfterHours({ timeProvided: _time });
        }
        
        // If _time is between 1200 and 1259, revert with message
        if (_time >= 1200 && _time <= 1259) {
            revert("At lunch!");
        }
        
        // Check other time conditions
        if (_time >= 800 && _time <= 1199) {
            return "Morning!";
        } else if (_time >= 1300 && _time <= 1799) {
            return "Afternoon!";
        } else if (_time >= 1800 && _time <= 2200) {
            return "Evening!";
        }
        
        // This is a fallback if none of the conditions are met
        // Although the above logic should cover all possibilities
        return "";
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/control-structures/control-structures-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵Arrays Pin NFT
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ArraysExercise {
    uint[] public numbers = [1,2,3,4,5,6,7,8,9,10];
    address[] public senders;
    uint[] public timestamps;

    // Return the complete numbers array
    function getNumbers() public view returns (uint[] memory) {
        return numbers;
    }

    // Reset numbers array to initial value (1-10)
    // Using more gas-efficient approach without .push()
    function resetNumbers() public {
        // Clear the array first
        delete numbers;
        // Recreate with initial values
        numbers = [1,2,3,4,5,6,7,8,9,10];
    }

    // Append array to existing numbers array
    function appendToNumbers(uint[] calldata _toAppend) public {
        for (uint i = 0; i < _toAppend.length; i++) {
            numbers.push(_toAppend[i]);
        }
    }

    // Save timestamp with caller address
    function saveTimestamp(uint _unixTimestamp) public {
        senders.push(msg.sender);
        timestamps.push(_unixTimestamp);
    }

    // Filter timestamps after Y2K (January 1, 2000, 12:00am)
    // Unix timestamp: 946702800
    function afterY2K() public view returns (uint[] memory, address[] memory) {
        // First, count how many timestamps are after Y2K
        uint count = 0;
        for (uint i = 0; i < timestamps.length; i++) {
            if (timestamps[i] > 946702800) {
                count++;
            }
        }

        // Create arrays with the correct size
        uint[] memory filteredTimestamps = new uint[](count);
        address[] memory filteredSenders = new address[](count);

        // Fill the arrays with filtered data
        uint index = 0;
        for (uint i = 0; i < timestamps.length; i++) {
            if (timestamps[i] > 946702800) {
                filteredTimestamps[index] = timestamps[i];
                filteredSenders[index] = senders[i];
                index++;
            }
        }

        return (filteredTimestamps, filteredSenders);
    }

    // Reset senders array
    function resetSenders() public {
        delete senders;
    }

    // Reset timestamps array
    function resetTimestamps() public {
        delete timestamps;
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/arrays/arrays-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵Storage Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract EmployeeStorage {
    // Custom error for too many shares
    error TooManyShares(uint256 totalShares);
    
    // State variables optimized for storage packing
    // Slot 0: shares (uint16) + salary (uint32) = 48 bits total
    uint16 private shares;      // Max 65,535 (enough for max 5,000 shares)
    uint32 private salary;      // Max 4.2 billion (enough for max 1,000,000)
    
    // Slot 1: name (string) - dynamic size, takes full slot
    string public name;
    
    // Slot 2: idNumber (uint256) - takes full slot  
    uint256 public idNumber;
    
    constructor(
        uint16 _shares,
        string memory _name, 
        uint32 _salary,
        uint256 _idNumber
    ) {
        shares = _shares;
        name = _name;
        salary = _salary;
        idNumber = _idNumber;
    }
    
    // View functions for private variables
    function viewSalary() public view returns (uint32) {
        return salary;
    }
    
    function viewShares() public view returns (uint16) {
        return shares;
    }
    
    // Grant shares function with validation
    function grantShares(uint16 _newShares) public {
        // Check if _newShares itself is greater than 5000
        if (_newShares > 5000) {
            revert("Too many shares");
        }
        
        uint256 totalShares = uint256(shares) + uint256(_newShares);
        
        // Check if total would exceed 5000
        if (totalShares > 5000) {
            revert TooManyShares(totalShares);
        }
        
        shares += _newShares;
    }
    
    function checkForPacking(uint _slot) public view returns (uint r) {
        assembly {
            r := sload (_slot)
        }
    }

    function debugResetShares() public {
        shares = 1000;
    }
}
```
## 🧾Instructions :

- Deploy with the following constructor parameters:

| **Parameter** | **Value** |
|----------------|-----------|
| shares | `1000` |
| name | `"Pat"` |
| salary | `50000` |
| idNumber | `112358132134` |

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/storage/storage-exercise
- Approve tx on wallet
- Verify on Guild base

# 🎯Base Learn Consul

## 🔵Mappings Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract FavoriteRecords {
    // Custom error
    error NotApproved(string albumName);
    
    // State variables
    mapping(string => bool) public approvedRecords;
    mapping(address => mapping(string => bool)) public userFavorites;
    
    // Array to keep track of approved record names for retrieval
    string[] private approvedRecordsList;
    
    constructor() {
        // Load approved records
        string[9] memory albums = [
            "Thriller",
            "Back in Black", 
            "The Bodyguard",
            "The Dark Side of the Moon",
            "Their Greatest Hits (1971-1975)",
            "Hotel California",
            "Come On Over",
            "Rumours",
            "Saturday Night Fever"
        ];
        
        for (uint i = 0; i < albums.length; i++) {
            approvedRecords[albums[i]] = true;
            approvedRecordsList.push(albums[i]);
        }
    }
    
    // Get all approved records
    function getApprovedRecords() public view returns (string[] memory) {
        return approvedRecordsList;
    }
    
    // Add record to user's favorites
    function addRecord(string memory albumName) public {
        if (!approvedRecords[albumName]) {
            revert NotApproved(albumName);
        }
        userFavorites[msg.sender][albumName] = true;
    }
    
    // Get user's favorite records
    function getUserFavorites(address user) public view returns (string[] memory) {
        string[] memory favorites = new string[](approvedRecordsList.length);
        uint count = 0;
        
        for (uint i = 0; i < approvedRecordsList.length; i++) {
            if (userFavorites[user][approvedRecordsList[i]]) {
                favorites[count] = approvedRecordsList[i];
                count++;
            }
        }
        
        // Create array with exact size
        string[] memory result = new string[](count);
        for (uint i = 0; i < count; i++) {
            result[i] = favorites[i];
        }
        
        return result;
    }
    
    // Reset user's favorites
    function resetUserFavorites() public {
        for (uint i = 0; i < approvedRecordsList.length; i++) {
            userFavorites[msg.sender][approvedRecordsList[i]] = false;
        }
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/mappings/mappings-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵Inheritance NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Abstract base contract
abstract contract Employee {
    uint public idNumber;
    uint public managerId;
    
    constructor(uint _idNumber, uint _managerId) {
        idNumber = _idNumber;
        managerId = _managerId;
    }
    
    function getAnnualCost() public view virtual returns (uint);
}

// Salaried employee contract
contract Salaried is Employee {
    uint public annualSalary;
    
    constructor(uint _idNumber, uint _managerId, uint _annualSalary) 
        Employee(_idNumber, _managerId) {
        annualSalary = _annualSalary;
    }
    
    function getAnnualCost() public view override returns (uint) {
        return annualSalary;
    }
}

// Hourly employee contract
contract Hourly is Employee {
    uint public hourlyRate;
    
    constructor(uint _idNumber, uint _managerId, uint _hourlyRate) 
        Employee(_idNumber, _managerId) {
        hourlyRate = _hourlyRate;
    }
    
    function getAnnualCost() public view override returns (uint) {
        return hourlyRate * 2080; // 2080 hours per year
    }
}

// Manager contract
contract Manager {
    uint[] public employeeIds;
    
    function addReport(uint _employeeId) public {
        employeeIds.push(_employeeId);
    }
    
    function resetReports() public {
        delete employeeIds;
    }
    
    function getEmployeeIds() public view returns (uint[] memory) {
        return employeeIds;
    }
}

// Salesperson contract (inherits from Hourly)
contract Salesperson is Hourly {
    constructor(uint _idNumber, uint _managerId, uint _hourlyRate) 
        Hourly(_idNumber, _managerId, _hourlyRate) {
    }
}

// Engineering Manager contract (multiple inheritance)
contract EngineeringManager is Salaried, Manager {
    constructor(uint _idNumber, uint _managerId, uint _annualSalary) 
        Salaried(_idNumber, _managerId, _annualSalary) {
    }
}

// Submission contract
contract InheritanceSubmission {
    address public salesPerson;
    address public engineeringManager;

    constructor(address _salesPerson, address _engineeringManager) {
        salesPerson = _salesPerson;
        engineeringManager = _engineeringManager;
    }
}
```

## 🧾 Instructions Parameter Deploy Contract :

### 1. Deploy **Salesperson**

| **Parameter** | **Value** |
|----------------|------------|
| _IDNUMBER | `55555` |
| _MANAGERID | `12345` |
| _HOURLYRATE | `20` |

---

### 2. Deploy **EngineeringManager**

| **Parameter** | **Value** |
|----------------|------------|
| _IDNUMBER | `54321` |
| _MANAGERID | `11111` |
| _ANNUALSALARY | `200000` |

---

### 3. Deploy **InheritanceSubmission**

| **Parameter** | **Value** |
|----------------|------------|
| _SALESPERSON | `[Paste Contract Address Salesperson]` |
| _ENGINEERINGMANAGER | `[Paste Contract Address EngineeringManager]` |

---

> 💡 **Note:**  
> - Replace `[Salesperson Contract Address]` and `[EngineeringManager Address]` with the contract addresses generated after you deploy them respectively  
> - Make sure you have selected the **Injected Provider - Coinbase Wallet** environment before deploying in Remix

- Compile yourfile.sol
- Select contract
- Deploy Salesperson | Fill parameter - Save contract
- Deploy EngineeringManager | Fill parameter | Save contract
- Deploy InheritanceSubmission | Fill parameter | Fill Contract Salesperson | Fill Contract EngineeringManager
- Copy last contract
- Paste contract : https://docs.base.org/learn/inheritance/inheritance-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵Structs Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract GarageManager {
    
    // Custom error for invalid car index
    error BadCarIndex(uint256 index);
    
    // Car struct with required properties
    struct Car {
        string make;
        string model;
        string color;
        uint256 numberOfDoors;
    }
    
    // Public mapping to store cars by owner address
    mapping(address => Car[]) public garage;
    
    // Add a car to the sender's garage
    function addCar(
        string memory _make,
        string memory _model,
        string memory _color,
        uint256 _numberOfDoors
    ) public {
        Car memory newCar = Car({
            make: _make,
            model: _model,
            color: _color,
            numberOfDoors: _numberOfDoors
        });
        
        garage[msg.sender].push(newCar);
    }
    
    // Get all cars owned by the calling user
    function getMyCars() public view returns (Car[] memory) {
        return garage[msg.sender];
    }
    
    // Get all cars for any given address
    function getUserCars(address _user) public view returns (Car[] memory) {
        return garage[_user];
    }
    
    // Update a car at specific index for the sender
    function updateCar(
        uint256 _index,
        string memory _make,
        string memory _model,
        string memory _color,
        uint256 _numberOfDoors
    ) public {
        // Check if sender has a car at that index
        if (_index >= garage[msg.sender].length) {
            revert BadCarIndex(_index);
        }
        
        // Update the car properties
        garage[msg.sender][_index] = Car({
            make: _make,
            model: _model,
            color: _color,
            numberOfDoors: _numberOfDoors
        });
    }
    
    // Reset the sender's garage (delete all cars)
    function resetMyGarage() public {
        delete garage[msg.sender];
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/structs/structs-exercise
- Approve tx on wallet
- Verify on Guild base

# 🎯Base Learn Perfect

## 🔵Error Triage Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ErrorTriageExercise {
    /**
     * Finds the difference between each uint with it's neighbor (a to b, b to c, etc.)
     * and returns a uint array with the absolute integer difference of each pairing.
     */
    function diffWithNeighbor(
        uint _a,
        uint _b,
        uint _c,
        uint _d
    ) public pure returns (uint[] memory) {
        uint[] memory results = new uint[](3);

        // Fix: Calculate absolute difference to avoid underflow
        results[0] = _a > _b ? _a - _b : _b - _a;
        results[1] = _b > _c ? _b - _c : _c - _b;
        results[2] = _c > _d ? _c - _d : _d - _c;

        return results;
    }

    /**
     * Changes the _base by the value of _modifier.  Base is always >= 1000.  Modifiers can be
     * between positive and negative 100;
     */
    function applyModifier(
        uint _base,
        int _modifier
    ) public pure returns (uint) {
        // Fix: Handle negative modifiers properly to avoid underflow
        if (_modifier >= 0) {
            return _base + uint(_modifier);
        } else {
            uint absModifier = uint(-_modifier);
            require(_base >= absModifier, "Result would be negative");
            return _base - absModifier;
        }
    }

    /**
     * Pop the last element from the supplied array, and return the popped
     * value (unlike the built-in function)
     */
    uint[] arr;

    function popWithReturn() public returns (uint) {
        require(arr.length > 0, "Array is empty");
        
        // Fix: Store the value before popping, then actually remove the element
        uint lastElement = arr[arr.length - 1];
        arr.pop(); // This actually removes the last element
        return lastElement;
    }

    // The utility functions below are working as expected
    function addToArr(uint _num) public {
        arr.push(_num);
    }

    function getArr() public view returns (uint[] memory) {
        return arr;
    }

    function resetArr() public {
        delete arr;
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/error-triage/error-triage-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵New Keyword Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "@openzeppelin/contracts/access/Ownable.sol";

contract AddressBook is Ownable {
    struct Contact {
        uint256 id;
        string firstName;
        string lastName;
        uint256[] phoneNumbers;
    }
    
    mapping(uint256 => Contact) public contacts;
    uint256[] public contactIds;
    uint256 private nextId = 1;
    
    error ContactNotFound(uint256 id);
    
    // Constructor with initial owner parameter
    constructor(address _initialOwner) Ownable(_initialOwner) {}
    
    function addContact(
        string memory _firstName,
        string memory _lastName,
        uint256[] memory _phoneNumbers
    ) external onlyOwner {
        uint256 contactId = nextId++;
        contacts[contactId] = Contact(contactId, _firstName, _lastName, _phoneNumbers);
        contactIds.push(contactId);
    }
    
    function deleteContact(uint256 _id) external onlyOwner {
        if (contacts[_id].id == 0) {
            revert ContactNotFound(_id);
        }
        delete contacts[_id];
        
        for (uint256 i = 0; i < contactIds.length; i++) {
            if (contactIds[i] == _id) {
                contactIds[i] = contactIds[contactIds.length - 1];
                contactIds.pop();
                break;
            }
        }
    }
    
    function getContact(uint256 _id) external view returns (Contact memory) {
        if (contacts[_id].id == 0) {
            revert ContactNotFound(_id);
        }
        return contacts[_id];
    }
    
    function getAllContacts() external view returns (Contact[] memory) {
        uint256 validCount = 0;
        for (uint256 i = 0; i < contactIds.length; i++) {
            if (contacts[contactIds[i]].id != 0) {
                validCount++;
            }
        }
        
        Contact[] memory result = new Contact[](validCount);
        uint256 index = 0;
        for (uint256 i = 0; i < contactIds.length; i++) {
            if (contacts[contactIds[i]].id != 0) {
                result[index] = contacts[contactIds[i]];
                index++;
            }
        }
        return result;
    }
}

contract AddressBookFactory {
    event AddressBookDeployed(address indexed newAddress, address indexed owner);
    
    function deploy() external returns (address) {
        // Deploy new AddressBook with msg.sender as owner
        AddressBook newAddressBook = new AddressBook(msg.sender);
        
        // Emit event for verification
        emit AddressBookDeployed(address(newAddressBook), msg.sender);
        
        return address(newAddressBook);
    }
}
```

## 🧾Instructions :
- After pressing Compile, select the deploy/run menu, change the contract and select AddressBookFactory then Deploy

- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/new-keyword/new-keyword-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵Imports Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

// Import the SillyStringUtils library
library SillyStringUtils {
    struct Haiku {
        string line1;
        string line2;
        string line3;
    }

    function shruggie(string memory _input) internal pure returns (string memory) {
        return string.concat(_input, unicode" 🤷");
    }
}

contract ImportsExercise {
    // Using the library
    using SillyStringUtils for string;
    
    // Public instance of Haiku
    SillyStringUtils.Haiku public haiku;
    
    // Save Haiku function
    function saveHaiku(
        string memory _line1, 
        string memory _line2, 
        string memory _line3
    ) public {
        haiku.line1 = _line1;
        haiku.line2 = _line2;
        haiku.line3 = _line3;
    }
    
    // Get Haiku function - returns the complete Haiku struct
    function getHaiku() public view returns (SillyStringUtils.Haiku memory) {
        return haiku;
    }
    
    function shruggieHaiku() public view returns (SillyStringUtils.Haiku memory) {
        SillyStringUtils.Haiku memory modifiedHaiku = SillyStringUtils.Haiku({
            line1: haiku.line1,
            line2: haiku.line2,
            line3: SillyStringUtils.shruggie(haiku.line3)
        });
        return modifiedHaiku;
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/imports/imports-exercise
- Approve tx on wallet
- Verify on Guild base

# 🎯Base Learn Supreme

## 🔵SCD ERC721 Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract HaikuNFT is ERC721 {
    // Custom errors
    error HaikuNotUnique();
    error NotYourHaiku(uint256 haikuId);
    error NoHaikusShared();

    // Structure for storing haiku data
    struct Haiku {
        address author;
        string line1;
        string line2;
        string line3;
    }

    // Public array to store all haiku
    Haiku[] public haikus;

    // Mapping to save shared haiku from address to haiku ID
    mapping(address => uint256[]) public sharedHaikus;

    // Counter for tracking total haiku and as next ID
    uint256 public counter = 1;

    // Mapping for tracking lines that have been used
    mapping(string => bool) private usedLines;

    constructor() ERC721("HaikuNFT", "HAIKU") {
        // Constructor kosong, ERC721 sudah menginisialisasi nama dan symbol
    }

    // Function for mint new haiku
    function mintHaiku(
        string memory _line1,
        string memory _line2,
        string memory _line3
    ) external {
        // Check whether one of the lines has been used before
        if (
            usedLines[_line1] ||
            usedLines[_line2] ||
            usedLines[_line3]
        ) {
            revert HaikuNotUnique();
        }

        // Mark lines as used
        usedLines[_line1] = true;
        usedLines[_line2] = true;
        usedLines[_line3] = true;

        // Create a new haiku
        Haiku memory newHaiku = Haiku({
            author: msg.sender,
            line1: _line1,
            line2: _line2,
            line3: _line3
        });

        // Add to array of haikus
        haikus.push(newHaiku);

        // Mint NFT with counter as tokenId
        _mint(msg.sender, counter);

        // Increment counter untuk ID berikutnya
        counter++;
    }

    // Function to share haiku to other addresses
    function shareHaiku(address _to, uint256 _haikuId) public {
        // Check if the sender is the owner of the haiku NFT
        if (ownerOf(_haikuId) != msg.sender) {
            revert NotYourHaiku(_haikuId);
        }

        // Add haiku ID to shared haikus from destination address
        sharedHaikus[_to].push(_haikuId);
    }

    // Function to get all haiku shared to caller
    function getMySharedHaikus() public view returns (Haiku[] memory) {
        uint256[] memory mySharedIds = sharedHaikus[msg.sender];
        
        // Check if there are any haiku shared
        if (mySharedIds.length == 0) {
            revert NoHaikusShared();
        }

        // Create a results array with a size according to the number of shared haikus.
        Haiku[] memory result = new Haiku[](mySharedIds.length);
        
        // Loop and fetch haiku data by ID
        for (uint256 i = 0; i < mySharedIds.length; i++) {
            // Because tokenId starts from 1, but array index from 0
            result[i] = haikus[mySharedIds[i] - 1];
        }

        return result;
    }

    // Helper function to get the total number of haiku that have been minted
    function getTotalHaikus() public view returns (uint256) {
        return haikus.length;
    }

    // Helper function to get haiku by ID
    function getHaiku(uint256 _haikuId) public view returns (Haiku memory) {
        require(_haikuId > 0 && _haikuId < counter, "Invalid haiku ID");
        return haikus[_haikuId - 1];
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/token-development/erc-721-token/erc-721-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵Minimal Token Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract UnburnableToken {
    // Storage variables
    mapping(address => uint256) public balances;
    uint256 public totalSupply;
    uint256 public totalClaimed;
    
    // Mapping to track which addresses have already claimed
    mapping(address => bool) public hasClaimed;
    
    // Custom errors
    error TokensClaimed();
    error AllTokensClaimed();
    error UnsafeTransfer(address _address);
    
    // Constructor - sets total supply to 100,000,000
    constructor() {
        totalSupply = 100_000_000; // No decimals, simple integer tokens
    }
    
    // Claim function - allows claiming 1000 tokens once per address
    function claim() public {
        // Check if all tokens have been claimed
        if (totalClaimed >= totalSupply) {
            revert AllTokensClaimed();
        }
        
        // Check if this address has already claimed
        if (hasClaimed[msg.sender]) {
            revert TokensClaimed();
        }
        
        // Mark address as having claimed
        hasClaimed[msg.sender] = true;
        
        // Add 1000 tokens to balance (no decimals)
        uint256 claimAmount = 1000;
        balances[msg.sender] += claimAmount;
        totalClaimed += claimAmount;
    }
    
    // Safe transfer function with safety checks
    function safeTransfer(address _to, uint256 _amount) public {
        // Check if recipient is not zero address
        if (_to == address(0)) {
            revert UnsafeTransfer(_to);
        }
        
        // Check if recipient has balance > 0 Base Sepolia ETH
        if (_to.balance == 0) {
            revert UnsafeTransfer(_to);
        }
        
        // Check if sender has sufficient balance
        require(balances[msg.sender] >= _amount, "Insufficient balance");
        
        // Perform transfer
        balances[msg.sender] -= _amount;
        balances[_to] += _amount;
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/token-development/minimal-tokens/minimal-tokens-exercise
- Approve tx on wallet
- Verify on Guild base

## 🔵ERC20 Pin NFTs
- Go to : https://remix.ethereum.org/
- Create new file or replace previous file
- Submit script :

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

contract WeightedVoting is ERC20 {
    using EnumerableSet for EnumerableSet.AddressSet;
    
    uint256 public constant maxSupply = 1000000; // 1 million tokens (no decimals for this exercise)
    
    error TokensClaimed();
    error AllTokensClaimed();
    error NoTokensHeld();
    error QuorumTooHigh(uint256 quorum);
    error AlreadyVoted();
    error VotingClosed();
    
    struct Issue {
        EnumerableSet.AddressSet voters;
        string issueDesc;
        uint256 votesFor;
        uint256 votesAgainst;
        uint256 votesAbstain;
        uint256 totalVotes;
        uint256 quorum;
        bool passed;
        bool closed;
    }
    

    Issue[] private issues;
    
    enum Vote {
        AGAINST,
        FOR,
        ABSTAIN
    }
    
    mapping(address => bool) public hasClaimed;
    
    constructor() ERC20("WeightedVoting", "WV") {
        
        issues.push();
        
        issues[0].issueDesc = "";
        issues[0].quorum = 0;
    }
    
    function decimals() public pure override returns (uint8) {
        return 0;
    }
    
    function claim() public {

        if (totalSupply() >= maxSupply) {
            revert AllTokensClaimed();
        }
        
        if (hasClaimed[msg.sender]) {
            revert TokensClaimed();
        }
        
        uint256 claimAmount = 100;
        
        if (totalSupply() + claimAmount > maxSupply) {
            revert AllTokensClaimed();
        }
        
        hasClaimed[msg.sender] = true;
        _mint(msg.sender, claimAmount);
    }
    
    function createIssue(string memory _issueDesc, uint256 _quorum) external returns (uint256) {

        if (balanceOf(msg.sender) == 0) {
            revert NoTokensHeld();
        }
        
        if (_quorum > totalSupply()) {
            revert QuorumTooHigh(_quorum);
        }
        
        issues.push();
        uint256 issueIndex = issues.length - 1;
        
        Issue storage newIssue = issues[issueIndex];
        newIssue.issueDesc = _issueDesc;
        newIssue.quorum = _quorum;
        newIssue.votesFor = 0;
        newIssue.votesAgainst = 0;
        newIssue.votesAbstain = 0;
        newIssue.totalVotes = 0;
        newIssue.passed = false;
        newIssue.closed = false;
        
        return issueIndex;
    }
    
    struct IssueView {
        address[] voters;
        string issueDesc;
        uint256 votesFor;
        uint256 votesAgainst;
        uint256 votesAbstain;
        uint256 totalVotes;
        uint256 quorum;
        bool passed;
        bool closed;
    }
    
    function getIssue(uint256 _id) external view returns (IssueView memory) {
        require(_id < issues.length, "Issue does not exist");
        
        Issue storage issue = issues[_id];
        
        uint256 voterCount = issue.voters.length();
        address[] memory voterList = new address[](voterCount);
        
        for (uint256 i = 0; i < voterCount; i++) {
            voterList[i] = issue.voters.at(i);
        }
        
        return IssueView({
            voters: voterList,
            issueDesc: issue.issueDesc,
            votesFor: issue.votesFor,
            votesAgainst: issue.votesAgainst,
            votesAbstain: issue.votesAbstain,
            totalVotes: issue.totalVotes,
            quorum: issue.quorum,
            passed: issue.passed,
            closed: issue.closed
        });
    }
    
    function vote(uint256 _issueId, Vote _vote) public {
        require(_issueId < issues.length && _issueId > 0, "Invalid issue ID");
        
        Issue storage issue = issues[_issueId];
        
        if (issue.closed) {
            revert VotingClosed();
        }
        
        if (issue.voters.contains(msg.sender)) {
            revert AlreadyVoted();
        }
        
        uint256 userTokens = balanceOf(msg.sender);
        if (userTokens == 0) {
            revert NoTokensHeld();
        }
        
        issue.voters.add(msg.sender);
        
        if (_vote == Vote.FOR) {
            issue.votesFor += userTokens;
        } else if (_vote == Vote.AGAINST) {
            issue.votesAgainst += userTokens;
        } else {
            issue.votesAbstain += userTokens;
        }
        
        issue.totalVotes += userTokens;
        
        if (issue.totalVotes >= issue.quorum) {
            issue.closed = true;
            
            if (issue.votesFor > issue.votesAgainst) {
                issue.passed = true;
            }
        }
    }
}
```

- Compile yourfile.sol
- Do the same thing as the first / previous tutorial
- Copy contract address
- Paste contract : https://docs.base.org/learn/token-development/erc-20-token/erc-20-exercise
- Approve tx on wallet
- Verify on Guild base

<hr>


