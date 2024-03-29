> 合约

```
// SPDX-License-Identifier: BSN
pragma solidity ^0.8.0;

import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

contract BsnOfficialRechargeEther is
    Initializable,
    OwnableUpgradeable,
    UUPSUpgradeable
{
    event BatchRecharge(
        address indexed operator,
        address[] toList,
        uint256[] amounts
    );

    event Receive(address indexed from, address indexed to, uint256 amount);

    constructor() payable initializer {}

    function initialize() public initializer {
        __Ownable_init();
        __UUPSUpgradeable_init();
    }

    function _authorizeUpgrade(address newImplementation)
        internal
        override
        onlyOwner
    {}

    function batchRecharge(address[] memory toList, uint256[] memory amounts)
        public
        payable
        onlyOwner
        returns (bool)
    {
        require(
            toList.length > 0 && toList.length == amounts.length,
            "BsnOfficialRechargeEther: Invalid parameters"
        );
        for (uint32 i = 0; i < toList.length; i++) {
            require(
                toList[i] != address(0),
                "BsnOfficialRechargeEther: zero address"
            );
            payable(toList[i]).transfer(amounts[i]);
        }
        emit BatchRecharge(_msgSender(), toList, amounts);
        return true;
    }

    function balance() public view returns (uint256) {
        return address(this).balance;
    }

    function version() public pure returns (string memory) {
        return "v1.0";
    }

    fallback() external payable {}

    receive() external payable {
        emit Receive(_msgSender(), address(this), msg.value);
    }
}
```

