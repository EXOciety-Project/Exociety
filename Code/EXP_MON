// SPDX-License-Identifier: MIT
pragma solidity 0.8.38;

/*
    ============================================================
                         EXOCIETY (EXO)
    ============================================================

    PROJECT PHILOSOPHY

    We left our former home behind in search of independence
    and fairness, setting out to build a new world of our own.

    Crypto is only a tool. It must never take precedence over
    trust and people.

    We will not compromise with powerful forces, seek their
    favor, lean on their influence, or depend on their support.
    We will neither bend to their will nor build our future on
    their terms. We will rely on no one but ourselves, and build
    a world that belongs to EXOciety alone.

    The founder has little interest in holding an excessive
    share of this token.

    Well... that's just how it is.

    Too much enthusiasm may make you want to jump into a river.

    The river is colder than you think.

    ============================================================
                         PROJECT PRINCIPLES
    ============================================================

    - Independence
    - Fairness
    - Trust and people before technology
    - No dependence on powerful external forces
    - Self-reliance
    - Transparency and responsibility

    ============================================================
                         TOKEN SPECIFICATION
    ============================================================

    Network       : Monad
    Token Name    : EXOciety
    Token Symbol  : EXO
    Decimals      : 18
    Initial Supply: 100,000,000,000 EXO
    Maximum Supply: 300,000,000,000 EXO
    Transfer Tax  : 0.2%
    Tax Payer     : Sender

    Owner Wallet  :
    0x35c2ABBE2157E882B37D3712cDf47a22f75A5177

    Tax Wallet    :
    0xc51033Db463BF82315422304240E9b814A5415ED

    ============================================================
                         TAX POLICY
    ============================================================

    1. All ordinary transfers are subject to a 0.2% tax.
    2. DEX-related transfers are also subject to the same tax.
    3. Transfers FROM the owner wallet are tax-exempt.
    4. Transfers FROM the tax wallet are tax-exempt.
    5. Transfers TO the owner wallet are taxable.
    6. Transfers TO the tax wallet are taxable.
    7. Minting and burning are tax-exempt.
    8. Tax is deducted from the sender's transfer amount.

    ============================================================
                         OWNERSHIP POLICY
    ============================================================

    1. The initial owner address is hardcoded.
    2. Ownership transfer is permanently disabled.
    3. Ownership renunciation remains available.
    4. Renouncing ownership permanently disables Mint and Burn.
    5. Ordinary ERC-20 transfers and Permit remain functional
       after ownership renunciation.

    ============================================================
*/

// OpenZeppelin Contracts 5.6.1
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";
import "@openzeppelin/contracts/access/Ownable.sol";


contract EXOciety is ERC20, ERC20Permit, Ownable {

    // ========================================================
    // FIXED WALLET ADDRESSES
    // ========================================================

    // Fixed initial owner and operator wallet.
    address public constant INITIAL_OWNER =
        0x35c2ABBE2157E882B37D3712cDf47a22f75A5177;

    // Fixed tax receiving wallet.
    address public constant TAX_WALLET =
        0xc51033Db463BF82315422304240E9b814A5415ED;


    // ========================================================
    // TOKEN SUPPLY
    // ========================================================

    // Initial issuance: 100 billion EXO.
    uint256 public constant INITIAL_SUPPLY =
        100_000_000_000 * 10**18;

    // Maximum circulating total supply: 300 billion EXO.
    // This limit is based on current totalSupply().
    uint256 public constant MAX_SUPPLY =
        300_000_000_000 * 10**18;


    // ========================================================
    // TRANSFER TAX
    // ========================================================

    // 0.2% = 20 / 10,000.
    uint256 public constant TAX_RATE = 20;

    uint256 public constant TAX_DENOMINATOR = 10_000;


    // ========================================================
    // MINT / BURN CONTROL
    // ========================================================

    // These switches are permanently disabled upon renunciation.
    bool public mintingEnabled = true;
    bool public burningEnabled = true;


    // ========================================================
    // EVENTS
    // ========================================================

    event TaxCollected(
        address indexed sender,
        address indexed recipient,
        uint256 taxAmount
    );

    event MintingPermanentlyDisabled();

    event BurningPermanentlyDisabled();


    // ========================================================
    // CUSTOM ERRORS
    // ========================================================

    error MintingPermanentlyDisabledError();

    error BurningPermanentlyDisabledError();

    error MaximumSupplyExceeded();

    error OwnershipTransferPermanentlyDisabled();


    // ========================================================
    // CONSTRUCTOR
    // ========================================================

    constructor()
        ERC20("EXOciety", "EXO")
        ERC20Permit("EXOciety")
        Ownable(INITIAL_OWNER)
    {
        // Mint the initial supply to the fixed owner wallet.
        _mint(INITIAL_OWNER, INITIAL_SUPPLY);
    }


    // ========================================================
    // DECIMALS
    // ========================================================

    function decimals() public pure override returns (uint8) {
        return 18;
    }


    // ========================================================
    // MINT
    // ========================================================

    // Only the fixed owner can mint additional EXO.
    // Minting is permanently disabled after renunciation.
    // Current totalSupply must never exceed MAX_SUPPLY.

    function mint(uint256 amount) external onlyOwner {

        if (!mintingEnabled) {
            revert MintingPermanentlyDisabledError();
        }

        if (totalSupply() + amount > MAX_SUPPLY) {
            revert MaximumSupplyExceeded();
        }

        _mint(owner(), amount);
    }


    // ========================================================
    // BURN
    // ========================================================

    // Only the owner can burn tokens from the owner's own balance.
    // Burning tokens held by other wallets is not permitted.
    // Burning is permanently disabled after renunciation.

    function burn(uint256 amount) external onlyOwner {

        if (!burningEnabled) {
            revert BurningPermanentlyDisabledError();
        }

        _burn(owner(), amount);
    }


    // ========================================================
    // OWNERSHIP TRANSFER CONTROL
    // ========================================================

    // Ownership transfer is permanently disabled.
    // The initial owner address cannot be replaced.
    // Ownership renunciation remains available.

    function transferOwnership(
        address newOwner
    ) public override onlyOwner {

        revert OwnershipTransferPermanentlyDisabled();
    }


    // ========================================================
    // OWNERSHIP RENUNCIATION
    // ========================================================

    // Once ownership is renounced:
    // - Mint is permanently disabled.
    // - Burn is permanently disabled.
    // - Ownership cannot be recovered.
    // - Ordinary token transfers remain functional.
    // - ERC20Permit remains functional.

    function renounceOwnership() public override onlyOwner {

        mintingEnabled = false;
        burningEnabled = false;

        emit MintingPermanentlyDisabled();
        emit BurningPermanentlyDisabled();

        super.renounceOwnership();
    }


    // ========================================================
    // TRANSFER TAX ENGINE
    // ========================================================

    // OpenZeppelin 5.x uses _update() as the central token
    // balance update mechanism.
    //
    // All ordinary transfers, including transfer() and
    // transferFrom(), pass through this function.
    //
    // Mint and burn are excluded from taxation.
    //
    // Sender-side tax exemptions:
    // - INITIAL_OWNER
    // - TAX_WALLET
    //
    // No recipient-side tax exemption is provided.

    function _update(
        address from,
        address to,
        uint256 amount
    ) internal override {

        // Minting and burning are tax-exempt.
        if (from == address(0) || to == address(0)) {
            super._update(from, to, amount);
            return;
        }

        // Transactions sent FROM the owner wallet are exempt.
        if (from == INITIAL_OWNER) {
            super._update(from, to, amount);
            return;
        }

        // Transactions sent FROM the tax wallet are exempt.
        if (from == TAX_WALLET) {
            super._update(from, to, amount);
            return;
        }

        // Calculate 0.2% tax.
        uint256 taxAmount =
            (amount * TAX_RATE) / TAX_DENOMINATOR;

        // Calculate the amount received by the recipient.
        uint256 receivedAmount = amount - taxAmount;

        // Transfer tax to the fixed tax wallet.
        if (taxAmount > 0) {

            super._update(
                from,
                TAX_WALLET,
                taxAmount
            );

            emit TaxCollected(
                from,
                to,
                taxAmount
            );
        }

        // Transfer the remaining amount to the recipient.
        super._update(
            from,
            to,
            receivedAmount
        );
    }
}
