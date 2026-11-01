// SPDX-License-Identifier: MIT
pragma solidity ^0.8.37;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract Exociety is ERC20, ERC20Permit, Ownable(msg.sender) {
    uint256 public constant INITIAL_SUPPLY = 100_000_000_000 * 10**18;

    // On-chain Notice Message (ASCII only)
    string public constant NOTICE =
        "This token was created purely for fun and as a hobby project. "
        "It may grow in the future, or it may stay exactly where it is. "
        "Don't take it too seriously - just enjoy it for simple fun. "
        "Wishing you happiness and good health always.";

    constructor()
        ERC20("Exociety", "EXO")
        ERC20Permit("Exociety")
    {
        _mint(msg.sender, INITIAL_SUPPLY);
    }

    // Owner-only mint (to owner wallet)
    function mint(uint256 amount) external onlyOwner {
        _mint(msg.sender, amount);
    }

    // Owner-only burn (from owner wallet)
    function burn(uint256 amount) external onlyOwner {
        _burn(msg.sender, amount);
    }
}
