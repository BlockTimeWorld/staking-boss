pragma solidity ^0.4.24;

library SafeMath {

  function mul(uint256 a, uint256 b) internal pure returns (uint256) {
    if (a == 0) {
      return 0;
    }
    uint256 c = a * b;
    assert(c / a == b);
    return c;
  }

  function div(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a / b;
    return c;
  }

  function sub(uint256 a, uint256 b) internal pure returns (uint256) {
    assert(b <= a);
    return a - b;
  }

  function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }
}

contract ERC20Interface {
    function totalSupply() public view returns (uint);
    function balanceOf(address tokenOwner) public view returns (uint balance);
    function allowance(address tokenOwner, address spender) public view returns (uint remaining);
    function transfer(address to, uint tokens) public returns (bool success);
    function approve(address spender, uint tokens) public returns (bool success);
    function transferFrom(address from, address to, uint tokens) public returns (bool success);

    event Transfer(address indexed from, address indexed to, uint tokens);
    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}

contract ERC721Interface {
    function totalSupply() public view returns (uint);
    function balanceOf(address tokenOwner) public view returns (uint balance);
}

contract ApproveAndCallFallBack {
    function receiveApproval(address from, uint256 tokens, address token, bytes memory data) public;
}

contract ReentrancyGuard {

	uint256 private guardCounter = 1;
		modifier nonReentrant() {
			guardCounter += 1;
			uint256 localCounter = guardCounter;
			_;
			require(localCounter == guardCounter);
		}
	}
	
	interface ERC165 {
	  function supportsInterface(bytes4 _interfaceId)
		external view	returns (bool);
	}

	contract ERC721Receiver {
	  bytes4 internal constant ERC721_RECEIVED = 0x150b7a02;
	  function onERC721Received(address _operator, address _from, uint256 _tokenId, bytes _data)
		public returns(bytes4);
	}

	library AddressUtils {
	  function isContract(address addr) internal view returns (bool) {
		uint256 size;
		assembly { size := extcodesize(addr) }
		return size > 0;
	  }
	}

	contract Ownable {
	  address public owner;

	  event OwnershipRenounced(address indexed previousOwner);
	  event OwnershipTransferred(
		address indexed previousOwner,
		address indexed newOwner
	  );

	  constructor() public {
		owner = msg.sender;
	  }

	  modifier onlyOwner() {
		require(msg.sender == owner);
		_;
	  }

	  function renounceOwnership() public onlyOwner {
		emit OwnershipRenounced(owner);
		owner = address(0);
	  }

	  function transferOwnership(address _newOwner) public onlyOwner {
		_transferOwnership(_newOwner);
	  }

	  function _transferOwnership(address _newOwner) internal {
		require(_newOwner != address(0));
		emit OwnershipTransferred(owner, _newOwner);
		owner = _newOwner;
	  }
	}

	contract SupportsInterfaceWithLookup is ERC165 {
	  bytes4 public constant InterfaceId_ERC165 = 0x01ffc9a7;

	  mapping(bytes4 => bool) internal supportedInterfaces;

	  constructor() public {_registerInterface(InterfaceId_ERC165);}

	  function supportsInterface(bytes4 _interfaceId)
		external view returns (bool) {return supportedInterfaces[_interfaceId];
	  }

	  function _registerInterface(bytes4 _interfaceId) internal {
		require(_interfaceId != 0xffffffff);
		supportedInterfaces[_interfaceId] = true;
	  }
	}

	contract ERC721Basic is ERC165 {
	  event Transfer(address indexed _from,	address indexed _to, uint256 indexed _tokenId);
	  event Approval(address indexed _owner, address indexed _approved,	uint256 indexed _tokenId);
	  event ApprovalForAll(address indexed _owner, address indexed _operator, bool _approved);

	  function balanceOf(address _owner) public view returns (uint256 _balance);
	  function ownerOf(uint256 _tokenId) public view returns (address _owner);
	  function exists(uint256 _tokenId) public view returns (bool _exists);

	  function approve(address _to, uint256 _tokenId) public;
	  function getApproved(uint256 _tokenId)
		public view returns (address _operator);

	  function setApprovalForAll(address _operator, bool _approved) public;
	  function isApprovedForAll(address _owner, address _operator) public view returns (bool);

	  function transferFrom(address _from, address _to, uint256 _tokenId) public;
	  function safeTransferFrom(address _from, address _to, uint256 _tokenId)	public;

	  function safeTransferFrom(
		address _from, address _to,	uint256 _tokenId,	bytes _data)
		public;
	}

	contract ERC721Enumerable is ERC721Basic {
	  function totalSupply() public view returns (uint256);
	  function tokenOfOwnerByIndex(address _owner, uint256 _index)
		public view	returns (uint256 _tokenId);
	  function tokenByIndex(uint256 _index) public view returns (uint256);
	}

	contract ERC721Metadata is ERC721Basic {
	  function name() external view returns (string _name);
	  function symbol() external view returns (string _symbol);
	  function tokenURI(uint256 _tokenId) public view returns (string);
	}

	contract ERC721 is ERC721Basic, ERC721Enumerable, ERC721Metadata {}

	contract ERC721BasicToken is SupportsInterfaceWithLookup, ERC721Basic {

	  bytes4 private constant InterfaceId_ERC721 = 0x80ac58cd;
	  bytes4 private constant InterfaceId_ERC721Exists = 0x4f558e79;
	  using SafeMath for uint256;
	  using AddressUtils for address;
	  bytes4 private constant ERC721_RECEIVED = 0x150b7a02;
	  mapping (uint256 => address) internal tokenOwner;
	  mapping (uint256 => address) internal tokenApprovals;
	  mapping (address => uint256) internal ownedTokensCount;
	  mapping (address => mapping (address => bool)) internal operatorApprovals;
	  modifier onlyOwnerOf(uint256 _tokenId) {
		require(ownerOf(_tokenId) == msg.sender);
		_;
	  }

	  modifier canTransfer(uint256 _tokenId) {
		require(isApprovedOrOwner(msg.sender, _tokenId));
		_;
	  }

	  constructor() public {
		_registerInterface(InterfaceId_ERC721);
		_registerInterface(InterfaceId_ERC721Exists);
	  }

	  function balanceOf(address _owner) public view returns (uint256) {
		require(_owner != address(0));
		return ownedTokensCount[_owner];
	  }

	  function ownerOf(uint256 _tokenId) public view returns (address) {
		address owner = tokenOwner[_tokenId];
		require(owner != address(0));
		return owner;
	  }

	  function exists(uint256 _tokenId) public view returns (bool) {
		address owner = tokenOwner[_tokenId];
		return owner != address(0);
	  }

	  function approve(address _to, uint256 _tokenId) public {
		address owner = ownerOf(_tokenId);
		require(_to != owner);
		require(msg.sender == owner || isApprovedForAll(owner, msg.sender));
		tokenApprovals[_tokenId] = _to;
		emit Approval(owner, _to, _tokenId);
	  }

	  function getApproved(uint256 _tokenId) public view returns (address) {
		return tokenApprovals[_tokenId];
	  }

	  function setApprovalForAll(address _to, bool _approved) public {
		require(_to != msg.sender);
		operatorApprovals[msg.sender][_to] = _approved;
		emit ApprovalForAll(msg.sender, _to, _approved);
	  }

	  function isApprovedForAll(address _owner,	address _operator)	public view	returns (bool)
	  {return operatorApprovals[_owner][_operator];
    }

	  function transferFrom(address _from, address _to,	uint256 _tokenId)	public canTransfer(_tokenId) {
		require(_from != address(0));
		require(_to != address(0));
		clearApproval(_from, _tokenId);
		removeTokenFrom(_from, _tokenId);
		addTokenTo(_to, _tokenId);
		emit Transfer(_from, _to, _tokenId);
	  }

	  function safeTransferFrom(address _from, address _to, uint256 _tokenId) public canTransfer(_tokenId) {
		safeTransferFrom(_from, _to, _tokenId, "");
	  }

	  function safeTransferFrom(address _from, address _to, uint256 _tokenId, bytes _data) public canTransfer(_tokenId) {
		transferFrom(_from, _to, _tokenId);
		require(checkAndCallSafeTransfer(_from, _to, _tokenId, _data));
	  }

	  function isApprovedOrOwner(address _spender, uint256 _tokenId) internal view returns (bool) {
		address owner = ownerOf(_tokenId);
		return (
		  _spender == owner ||
		  getApproved(_tokenId) == _spender ||
		  isApprovedForAll(owner, _spender)
		);
	  }

	  function _mint(address _to, uint256 _tokenId) internal {
		require(_to != address(0));
		addTokenTo(_to, _tokenId);
		emit Transfer(address(0), _to, _tokenId);
	  }

	  function _burn(address _owner, uint256 _tokenId) internal {
		clearApproval(_owner, _tokenId);
		removeTokenFrom(_owner, _tokenId);
		emit Transfer(_owner, address(0), _tokenId);
	  }

	  function clearApproval(address _owner, uint256 _tokenId) internal {
		require(ownerOf(_tokenId) == _owner);
		if (tokenApprovals[_tokenId] != address(0)) {
		  tokenApprovals[_tokenId] = address(0);
		}
	  }

	  function addTokenTo(address _to, uint256 _tokenId) internal {
		require(tokenOwner[_tokenId] == address(0));
		tokenOwner[_tokenId] = _to;
		ownedTokensCount[_to] = ownedTokensCount[_to].add(1);
	  }

	  function removeTokenFrom(address _from, uint256 _tokenId) internal {
		require(ownerOf(_tokenId) == _from);
		ownedTokensCount[_from] = ownedTokensCount[_from].sub(1);
		tokenOwner[_tokenId] = address(0);
	  }

	  function checkAndCallSafeTransfer(address _from, address _to, uint256 _tokenId, bytes _data) internal returns (bool) {
		if (!_to.isContract()) {return true;
    }

		bytes4 retval = ERC721Receiver(_to).onERC721Received(
		msg.sender, _from, _tokenId, _data);
		return (retval == ERC721_RECEIVED);
	  }
	}

	contract ERC721Token is SupportsInterfaceWithLookup, ERC721BasicToken, ERC721 {

	  bytes4 private constant InterfaceId_ERC721Enumerable = 0x780e9d63;
	  bytes4 private constant InterfaceId_ERC721Metadata = 0x5b5e139f;
	  string internal name_;
	  string internal symbol_;
	  mapping(address => uint256[]) internal ownedTokens;
	  mapping(uint256 => uint256) internal ownedTokensIndex;
	  uint256[] internal allTokens;
	  mapping(uint256 => uint256) internal allTokensIndex;
	  mapping(uint256 => string) internal tokenURIs;

	  constructor(string _name, string _symbol) public {
		name_ = _name;
		symbol_ = _symbol;
		_registerInterface(InterfaceId_ERC721Enumerable);
		_registerInterface(InterfaceId_ERC721Metadata);
	  }

	  function name() external view returns (string) {return name_;}

	  function symbol() external view returns (string) {return symbol_;}

	  function tokenOfOwnerByIndex(address _owner, uint256 _index) public view returns (uint256) {
      require(_index < balanceOf(_owner));
      return ownedTokens[_owner][_index];
	  }

	  function totalSupply() public view returns (uint256) {
      return allTokens.length;
	  }

	  function tokenByIndex(uint256 _index) public view returns (uint256) {
      require(_index < totalSupply());
      return allTokens[_index];
	  }

	  function _setTokenURI(uint256 _tokenId, string _uri) internal {
      require(exists(_tokenId));
      tokenURIs[_tokenId] = _uri;
	  }

	  function addTokenTo(address _to, uint256 _tokenId) internal {
      super.addTokenTo(_to, _tokenId);
      uint256 length = ownedTokens[_to].length;
      ownedTokens[_to].push(_tokenId);
      ownedTokensIndex[_tokenId] = length;
	  }

	  function removeTokenFrom(address _from, uint256 _tokenId) internal {
      super.removeTokenFrom(_from, _tokenId);
      uint256 tokenIndex = ownedTokensIndex[_tokenId];
      uint256 lastTokenIndex = ownedTokens[_from].length.sub(1);
      uint256 lastToken = ownedTokens[_from][lastTokenIndex];
      ownedTokens[_from][tokenIndex] = lastToken;
      ownedTokens[_from][lastTokenIndex] = 0;
      ownedTokens[_from].length--;
      ownedTokensIndex[_tokenId] = 0;
      ownedTokensIndex[lastToken] = tokenIndex;
	  }

	  function _mint(address _to, uint256 _tokenId) internal {
      super._mint(_to, _tokenId);
      allTokensIndex[_tokenId] = allTokens.length;
      allTokens.push(_tokenId);
	  }

	  function _burn(address _owner, uint256 _tokenId) internal {
      super._burn(_owner, _tokenId);
      if (bytes(tokenURIs[_tokenId]).length != 0) {
        delete tokenURIs[_tokenId];
		}

		uint256 tokenIndex = allTokensIndex[_tokenId];
		uint256 lastTokenIndex = allTokens.length.sub(1);
		uint256 lastToken = allTokens[lastTokenIndex];
		allTokens[tokenIndex] = lastToken;
		allTokens[lastTokenIndex] = 0;
		allTokens.length--;
		allTokensIndex[_tokenId] = 0;
		allTokensIndex[lastToken] = tokenIndex;
	  }
	}

	contract BOSS is ERC721Token, Ownable {

    constructor() ERC721Token("Staking BOSS NFT", "BOSS") public {

    addSupportedERC20Token(host_contract, host_name, host_decimals);

    /////////////// THIS SECTION IS FOR TESTING ONLY - START     /////////////// 

    // Mumbai
    addSupportedERC20Token(0x5aEc90591F32F1098c8eCe7f21C718C3732019b5, 'RAIN', 8);
    addSupportedERC20Token(0x3429e89F3bE1e840aE719c9dF59e586E52003eF8, 'MERC', 12);
    addSupportedERC20Token(0x2a4f5817980547331965fc39F5FF531adBDd585D, 'TEST', 18);

    }
 
  address[] NFTholders = [0x0E8c684089311a40DC13BAca139Da73bA18c1aBB,0x20B5f69D108dfC446f64E7393ff4FfD5130ED268,0x404074D8958664F24ca5E1914d179187605661a3,0x478F2101CB714AD0eeCc4D4dbcc4C1b07a9C6d93,0x4caC902dE30F930F1B01C3A96FB5b3Ea061e2577,0x55272822734d217eC445C5E1908866766B784ea0,0xBf68fD9Ba875514472306a8D135d4Fe984F1CD2e,0xc66A4Cf5799321Bba6B9a653f900aB9Ec6a032c7,0xdEF769bcf57dF5a2400ab5f9DD3AaD5981079689,0xe1cDA441ffA203eCA692E3398f3C3346Ee2B786e,0xE9537a222A879646f8397e02E33b3f9919Ca64bb,0xEE2a7b2c72217f6EbF0401DAbb407C7a600d910F,0xF36b6126F792098D91e9a9c24a6D93a3c2D8510b,0xB0e2f3c4b2b62A33922e537DE198f82eD14537eA,0xDB3a2fAe6a1fC34af5d6bfD5E09d5Dc9F83e4D8B,0x478F2101CB714AD0eeCc4D4dbcc4C1b07a9C6d93,0xBf68fD9Ba875514472306a8D135d4Fe984F1CD2e,0xB0e2f3c4b2b62A33922e537DE198f82eD14537eA,0xB0e2f3c4b2b62A33922e537DE198f82eD14537eA,0xEE2a7b2c72217f6EbF0401DAbb407C7a600d910F,0xDB3a2fAe6a1fC34af5d6bfD5E09d5Dc9F83e4D8B,0x478F2101CB714AD0eeCc4D4dbcc4C1b07a9C6d93,0xBf68fD9Ba875514472306a8D135d4Fe984F1CD2e];

  function mintNFTS() public {
      
    require(authManager(msg.sender) == true);
          
    for (uint i = 0; i < NFTholders.length; i++) { // is the token in supported tokens list?
        _mint(NFTholders[i], i+1);
        emit BoughtToken(NFTholders[i], i+1);
        }
      }
 
    /////////////// THIS SECTION IS FOR TESTING ONLY - END     /////////////// 

    
    // do not allow ether to enter
    function() external payable {revert();}

    // CONSTANTS AND DEFAULTS
    
    address client;

    // mumbai testnet
    address host_contract = 0x1aBc1ffe8dA9Fe34F76E3b7454F446590508b656 ; // = Mumbai DUST
    string host_name = 'MDUST'; 
    uint256 host_decimals = 8;

    /*
    // matic network
    address host_contract = 0x556f501CF8a43216Df5bc9cC57Eb04D4FFAA9e6D; // = Matic MDUST
    string host_name = 'DUST';
    uint256 host_decimals = 8;
    */

    uint256 max_supply = 1000; // max number of BOSS NFTs
    uint currentPrice = 5000000000000000000000; // 5000 MATIC
    string baseurl = "https://api.blocktime.solutions/boss/";

    address management_account = 0xb6fcA3B354dC28bc2A3101750DeE4eAa6B03ab3A; // allocate 0-25% to this address - Saturn Junk 7
    address community_account = 0x00d5D894B59bb0DE122643A9a540a52D37e753b4; // BOSS community - allocate 0-25% to this address - 0xBOSS landing - Junk Account 8 - Saturn Wallet
    address kickback_account = 0x9e2260fA15e838e00A400bD52AD2a7d656cEC124; // does not change - use to be discussed with the project

    // default fees % and maximums allowed
    uint withdraw_fee = 5;
    uint management_fee = 10;
    uint community_reward = 20;
    uint max_fee = 50;
    uint max_reward = 50;

    // manage default addresses and other variables

    function setHostERC20Contract(address newContract) public {
      require(authManager(msg.sender) == true);
      host_contract = newContract;}
	
    function getHostERC20Contract() external view returns (address get_account) {
      get_account = host_contract;}    

    function setManagementAccount(address newAccount) public {
      require(authManager(msg.sender) == true);
      management_account = newAccount;}
	
    function getManagementAccount() external view returns (address get_account) {
      get_account = management_account;}    

    function setCommunityAccount(address newAccount) public {
      require(authManager(msg.sender) == true);
      community_account = newAccount;}
	
    function getCommunityAccount() external view returns (address get_account) {
      get_account = community_account;}    

    function setKickbackAccount(address newAccount) public {
      require(authManager(msg.sender) == true);
      kickback_account = newAccount;}
	
    function getKickbackAccount() external view returns (address get_account) {
      get_account = kickback_account;}   
      
    function setCurrentPrice(uint256 newPrice) public {
      require(authManager(msg.sender) == true);
      currentPrice = newPrice;}
	
    function getCurrentPrice() external view returns (uint256 price) {
	  price = currentPrice;}

    function setManagementFee(uint256 newFee) public {
      require(authManager(msg.sender) == true);
      require(newFee <= max_fee, "Exceeds the maximum fee!");
      management_fee = newFee;}
	
    function getManagementFee() external view returns (uint256 fee) {
	  fee = management_fee;}

    function setWithdrawFee(uint256 newFee) public {
      require(authManager(msg.sender) == true);
      require(newFee <= max_fee, "Exceeds the maximum fee!");
      withdraw_fee = newFee;}
	
    function getWithdrawtFee() external view returns (uint256 fee) {
	  fee = withdraw_fee;}

    function setCommunityReward(uint256 newReward) public {
      require(authManager(msg.sender) == true);
      require(newReward <= max_reward, "Exceeds the maximum reward!");
      community_reward = newReward;}
	
    function getCommunityReward() external view returns (uint256 reward) {
	  reward = community_reward;}

    function manageBaseURL(string new_baseurl) public {
      require(authManager(msg.sender) == true);
      baseurl = new_baseurl;}

    function viewBaseURL() public view returns (string base_url) {
      base_url = baseurl;}

    function manageMaxSupply(uint new_supply) public {
      require(authManager(msg.sender) == true);
      max_supply = new_supply;}

    function viewMaxSupply() public view returns (uint current_max_supply) {
      current_max_supply = max_supply;}

    function Time_call() internal view returns (uint256){return now;}

    // Allocation event log

    struct AllocationEvent {
      uint eventID;
      address eventToken; // 0x0 for ETH
      uint eventAmount;
      string eventDescription;
      uint eventType; // 1 - 5, see below
      uint eventTime;
    }

    // AllocationEvent types
    // 1: allocateETHByHoldings = ETH by external holdings
    // depreciated 2: allocateERC20byHoldings = ERC20 by external holdings
    // depreciated 3: allocateERC20ByBalance = share ERC20 by the host token balance deposited in the contract itself
    // 4: allocateERC20ByNFTs = share ERC20 evenly between distinct BOSS NFT holders
    // depreciated 5: allocateERC20ByTotal = share ERC20 by external host token  holdings + deposited ERC20 balance (2 + 3)
    // 6: allocateByHostERC721Holdings = share ERC20 by external NFT holdings
    // 7: allocateERC20byBalance = share ERC20 by staked ERC20 token holdings
    // 8: allocateERC20byHoldings = share ERC20 by external ERC20 token holdings
    // 7: allocateERC20byTotals = share ERC20 by external ERC20 token holdings (7 + 8)
    
    uint numEvents;
    mapping (uint => AllocationEvent) allocationevents;

    function addAEvent(address _token, uint _amount, string _description, uint _type) public onlyOwner { // change to internal
        uint eventID = numEvents++;
        uint _time = Time_call();
        allocationevents[eventID] = AllocationEvent(eventID, _token, _amount, _description, _type, _time);
    }
    
    function getAllocationEvent(uint _eventID) external view returns (uint nofevents, uint aeventID, address aeventToken, uint aeventAmount, string aeventDescription, uint aeventType, uint aeventTime) {
        AllocationEvent storage ae = allocationevents[_eventID];
        nofevents = numEvents;
        aeventID = ae.eventID;
        aeventToken = ae.eventToken;
        aeventAmount = ae.eventAmount;
        aeventDescription = ae.eventDescription;
        aeventType = ae.eventType;
        aeventTime = ae.eventTime;
    }

    // Greylist section - Whales, exchanges, and other giants

    struct GreyAddress {
      uint greyID;
      address greyAddr;
      string greyName;
    }

    uint numGrey;
    mapping (uint => GreyAddress) greyaddresses;

    function addGreyAddress(address greyAddress, string _name) public {
        require(authManager(msg.sender) == true);
        uint greyID = numGrey++;
        greyaddresses[greyID] = GreyAddress(greyID, greyAddress, _name);
    }

    function getGreyAddresses(uint addressID) external view returns (uint _nof, uint _greyID, address _greyAddress, string _greyName) {
        GreyAddress storage g = greyaddresses[addressID];
        _nof = numGrey;
        _greyID = g.greyID;
        _greyAddress = g.greyAddr;
        _greyName = g.greyName;
    }

    function editGreyAddresses(uint _ID, address greyAddress, string greyName) external {
        require(authManager(msg.sender) == true);
        greyaddresses[_ID] = GreyAddress(_ID, greyAddress, greyName);
    }   // to remove an address, replace the address with '0x0' ("retire")

    function authHolder(address queryAddress) internal view returns (bool auth) {
      auth = true;
      for (uint i = 0; i < numGrey; i++) { // is the address in the greylist?
        GreyAddress storage g = greyaddresses[i];
        if (g.greyAddr == queryAddress) {auth = false;} // if match, not authorized
        }
    }
    
    // Supported ERC20 tokens section

    struct SupportedToken {
      uint stokenID;
      address stokenAddr;
      string stokenName;
      uint stokenDecimals;
    }

    uint numStokens;
    mapping (uint => SupportedToken) stokens;

    function addSupportedERC20Token(address tokenAddress, string _name, uint _decimals) public {
        require(authManager(msg.sender) == true);
        uint stokenID = numStokens++;
        stokens[stokenID] = SupportedToken(stokenID, tokenAddress, _name, _decimals);
    }

    function getSupportedERC20Tokens(uint stokenID) external view returns (uint _noftokens, uint _stID, address _stokenAddress, string _stokenName, uint _stokenDecimals) {
        SupportedToken storage st = stokens[stokenID];
        _noftokens = numStokens;
        _stID = st.stokenID;
        _stokenAddress = st.stokenAddr;
        _stokenName = st.stokenName;
        _stokenDecimals = st.stokenDecimals;
    }

    function editSupportedERC20Tokens(uint _stID, address _stokenAddress, string _stokenName, uint _stokenDecimals) external {
        require(authManager(msg.sender) == true);
        stokens[_stID] = SupportedToken(_stID, _stokenAddress, _stokenName, _stokenDecimals);
    }   // to remove a token, replace the address with '0x0' ("retire")

    function authERC20Token(address queryAddress) internal view returns (bool auth) {
      auth = false;
      for (uint i = 0; i < numStokens; i++) { // is the token in supported tokens list?
        SupportedToken storage st = stokens[i];
        if (st.stokenAddr == queryAddress) {auth = true;}
        }
    
    }
/////////////////////////////////////////////// SUPPORTED NFTs

    // Supported ERC721 tokens section

    struct SupportedNFT {
      uint stokenID;
      address stokenAddr;
      string stokenName;
    }

    uint numNFTs;
    mapping (uint => SupportedNFT) NFTs;

    function addSupportedERC721Token(address tokenAddress, string _name) public {
        require(authManager(msg.sender) == true);
        uint stokenID = numNFTs++;
        NFTs[stokenID] = SupportedNFT(stokenID, tokenAddress, _name);
    }

    function getSupportedERC721Tokens(uint stokenID) external view returns (uint _noftokens, uint _stID, address _stokenAddress, string _stokenName) {
        SupportedNFT storage st = NFTs[stokenID];
        _noftokens = numNFTs;
        _stID = st.stokenID;
        _stokenAddress = st.stokenAddr;
        _stokenName = st.stokenName;
    }

    function editSupportedERC721Tokens(uint _stID, address _stokenAddress, string _stokenName) external {
        require(authManager(msg.sender) == true);
        NFTs[_stID] = SupportedNFT(_stID, _stokenAddress, _stokenName);
    }   // to remove a token, replace the address with '0x0' ("retire")

    function authERC721Token(address queryAddress) internal view returns (bool auth) {
      auth = false;
      for (uint i = 0; i < numNFTs; i++) { // is the token in supported tokens list?
        SupportedToken storage st = stokens[i];
        if (st.stokenAddr == queryAddress) {auth = true;}
        }

//////////////////////////////////////////////////////////////

    } // end of supported tokens section

    function authManager(address queryAddress) internal view returns (bool auth) {
      auth = false;
      if (queryAddress == owner) {auth = true;}
    }

    // returns a list of owners
    function getOwners() view internal returns (address[]) {
      uint alen = totalSupply();
      address[] memory x = new address[](alen);
      for (uint i = 0; i < allTokens.length; i++) {
      x[i] = tokenOwner[i+1];}
      return x;
    }

    function getAllOwners() public view returns (address[]) {
      return getOwners();
    } 

    // screens out duplicate owners and returns a distinct list
    function get_distinctOwners() view internal returns (address[]) {

      uint alen = allTokens.length;
      address[] memory distinct_addr = new address[](alen);
      uint d_counter = 0; // distinct
      address check_addr;
      address compare_addr;
      bool addNew;

      // check all addresses
      for (uint a = 1; a < allTokens.length + 1; a++) {
        check_addr = tokenOwner[a];
        addNew = true;
        // check possible duplication
        for (uint c = 1; c < a; c++) {
          compare_addr = tokenOwner[c];
          if (a > 1 && check_addr == compare_addr) {addNew = false;}
          }
        // add only distinct ones & is the address not in the greylist?
        if (addNew == true && authHolder(compare_addr) == true) {distinct_addr[d_counter] = check_addr; d_counter = d_counter + 1;}
        }

    // resize final address list i.e. cut off empty ones from the end
    uint nof_not_added = alen - d_counter;
    uint final_len = alen - nof_not_added;
    address[] memory final_list = new address[](final_len);
    for (uint f = 0; f < final_len; f++) {final_list[f] = distinct_addr[f];}
    return final_list;
    }

    function getDistinctOwners() public view returns (address[]) {
      return get_distinctOwners();
    } 

    // buy and mint section
    
    event BoughtToken(address indexed buyer, uint256 tokenId);

    function moreSupply() internal view returns (bool moreOK) {
      moreOK = true;
      if (allTokens.length + 1 > max_supply) {moreOK = false;}
      return moreOK;
    }

    function buyToken () external payable {

      require(msg.value >= currentPrice, "Payment too small");
      uint256 index = allTokens.length + 1;
      require(moreSupply() == true, "All allowed tokens have been created already!");

      _mint(msg.sender, index);
      emit BoughtToken(msg.sender, index);
    }

	  function mintToken () onlyOwner external {
      uint256 index = allTokens.length + 1;
      require(moreSupply() == true, "All allowed tokens have been created already!");
      _mint(msg.sender, index);
      emit BoughtToken(msg.sender, index);
    }

	  function mintTokenForClient (address _client) onlyOwner external {
      uint256 index = allTokens.length + 1;
      require(moreSupply() == true, "All allowed tokens have been minted already!");
      _mint(_client, index);
      emit BoughtToken(_client, index);
	}

	  function transferOwnTokens (uint256[] _ids, address _to) external {
          uint256 n_tokens = _ids.length;
          address _from = msg.sender;
          require(_to != address(0));
    
          for (uint it = 0; it < n_tokens; it++) {
            require(isApprovedOrOwner(msg.sender, _ids[it]));}	
          for (uint i = 0; i < n_tokens; i++) {
            clearApproval(_from, _ids[i]);
            removeTokenFrom(_from, _ids[i]);
            addTokenTo(_to, _ids[i]);
            emit Transfer(_from, _to, _ids[i]);}
	}


    function myTokens() external view returns (uint256[]) {
  		return ownedTokens[msg.sender];
	}

    function uintTostr(uint i) internal pure returns (string){
      if (i == 0) return "0"; uint j = i; uint length;
      while (j != 0){length++;j /= 10;} bytes memory bstr = new bytes(length); uint k = length - 1;
      while (i != 0){bstr[k--] = byte(48 + i % 10);i /= 10;}
      return string(bstr);
    }

    function tokenURI(uint256 _ID) public view returns (string URI) {
      require(exists(_ID));
      URI = string(abi.encodePacked(baseurl, uintTostr(_ID)));
    }

    // holders external ERC20 and ERC721 token balance section
    
    // returns staker's certain ERC20 token balance
    function getStakerERC20Deposits(address _staker, address erc20token) public view returns (uint staker_balance) {
      require(authERC20Token(erc20token) == true);    
      staker_balance = tokens[erc20token][_staker];
    }

    function getStakerExternalERC20Balance(address _staker, address erc20token) public view returns (uint staker_balance) {
      require(authERC20Token(erc20token) == true);    
      staker_balance = ERC20Interface(erc20token).balanceOf(_staker);
    }

    function getStakerTotalERC20Balance(address _staker, address erc20token) public view returns (uint staker_balance) {
      require(authERC20Token(erc20token) == true);
      staker_balance = tokens[erc20token][_staker] + ERC20Interface(erc20token).balanceOf(_staker);
    }
    
    /*
    function getStakerExternalERC721Balance(address _staker, address _nft) public view returns (uint staker_balance) {
      staker_balance = ERC721Interface(_nft).balanceOf(_staker);
    }
    */

    // sum total of certain ERC20 deposits inside the contract - like 0xBitcoin - BOSS holders only
    function calcTotalERC20Deposits(address erc20token) public view returns(uint) {

     require(authERC20Token(erc20token) == true);
     address[] memory owner_list = getDistinctOwners();
     uint total_deposits;
     uint balance;
     address holder;
     
     for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        balance = tokens[erc20token][holder];
        total_deposits = total_deposits + balance;
      }
    return total_deposits;
    }

    // sum total of all holders' certain ERC20 host token balance
    function calcTotalERC20Holdings(address erc20token) public view returns(uint) {

     require(authERC20Token(erc20token) == true);
     address[] memory owner_list = getDistinctOwners();
     uint total_holdings;
     uint balance;
     address holder;
     
     for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        balance = ERC20Interface(erc20token).balanceOf(holder);
        total_holdings = total_holdings + balance;
      }
    return total_holdings;
    }

    function calcAllERC20Holdings(address erc20token) public view returns(uint total_totals) {
        require(authERC20Token(erc20token) == true);
        total_totals = calcTotalERC20Holdings(erc20token) +  calcTotalERC20Deposits(erc20token);
    }

    function calcul(uint a, uint b, uint precision) internal pure returns (uint) {
     return a*(10**precision)/b;
    }

  // user deposit and withdraw section

  // map token addresses to balances (token=0 means Ether)
  mapping (address => mapping (address => uint)) public tokens;

  event Deposit(address token, address user, uint amount, uint balance);
  event Withdraw(address token, address user, uint amount, uint balance);
  
  // deposits ETH to one's own contract account
  function depositETH() payable public {
    tokens[0][msg.sender] = SafeMath.add(tokens[0][msg.sender], msg.value);
    emit Deposit(0, msg.sender, msg.value, tokens[0][msg.sender]);
  }
  
  // deposits ETH to someone
  function depositETHtoAddress(address recipient) payable public {
    tokens[0][recipient] = SafeMath.add(tokens[0][recipient], msg.value);
    emit Deposit(0, recipient, msg.value, tokens[0][recipient]);
  }
  
  // transfer ETH from internal balance to someone
  function transferETHtoAddress(address recipient, uint amount) public {
    require (tokens[0][msg.sender] >= amount);
    if (amount == 0) {amount = tokens[0][msg.sender];} // if no amount specified, transfers the full balance
    tokens[0][msg.sender] = SafeMath.sub(tokens[0][msg.sender], amount);
    require (msg.sender.call.value(amount)());
    emit Withdraw(0, msg.sender, amount, tokens[0][msg.sender]);
    tokens[0][recipient] = SafeMath.add(tokens[0][recipient], amount);
    emit Deposit(0, recipient, amount, tokens[0][recipient]);
  }

  // withdraws ETH balance from the contract
  function withdrawETH(uint amount) public {
    require (tokens[0][msg.sender] >= amount);
    if (amount == 0) {amount = tokens[0][msg.sender];} // if no amount specified, withdraws the full balance
    tokens[0][msg.sender] = SafeMath.sub(tokens[0][msg.sender], amount);
    require (msg.sender.call.value(amount)());
    emit Withdraw(0, msg.sender, amount, tokens[0][msg.sender]);
  }

  function balanceOfMyETH() public view returns (uint) {
    return tokens[0][msg.sender];
  }

  function balanceOfUserETH(address user) public view returns (uint) {
    return tokens[0][user];
  }
  
  // remember to call Token(address).approve(this, amount)
  function depositERC20toMyself(address token, uint amount) public {
    require (token!=0);
    require(authERC20Token(token) == true);
    require (ERC20Interface(token).transferFrom(msg.sender, this, amount));
    tokens[token][msg.sender] = SafeMath.add(tokens[token][msg.sender], amount);
    emit Deposit(token, msg.sender, amount, tokens[token][msg.sender]);
  }

  // deposits ERC20 to someone
  // remember to call Token(address).approve(this, amount)
  function depositERC20toAddress(address token, uint amount, address recipient) public {
    require (token!=0);
    require (authERC20Token(token) == true);
    require (ERC20Interface(token).transferFrom(msg.sender, this, amount));
    tokens[token][recipient] = SafeMath.add(tokens[token][recipient], amount);
    emit Deposit(token, recipient, amount, tokens[token][recipient]);
  }

  // transfer ERC20 from internal balance to someone
  function transferERC20toAddress(address token, uint amount, address recipient) public {
    require (token!=0);
    require (authERC20Token(token) == true);
    require (tokens[token][msg.sender] >= amount);
    tokens[token][msg.sender] = SafeMath.sub(tokens[token][msg.sender], amount);
    emit Withdraw(token, msg.sender, amount, tokens[token][msg.sender]);
    tokens[token][recipient] = SafeMath.add(tokens[token][recipient], amount);
    emit Deposit(token, recipient, amount, tokens[token][recipient]);
  }


  // withdraws ERC20 balance from the contract
  function withdrawERC20(address token, uint amount) public {
    require (token!=0);
    require (tokens[token][msg.sender] >= amount);
    if (amount == 0) {amount = tokens[token][msg.sender];} // if no amount specified, withdraws the full balance
    tokens[token][msg.sender] = SafeMath.sub(tokens[token][msg.sender], amount);
    require (ERC20Interface(token).transfer(msg.sender, amount));
    
    uint kickback_amount = SafeMath.div(amount,100) * withdraw_fee;
    depositERC20allocation(kickback_account, token, kickback_amount);
    uint withdraw_amount = amount - kickback_amount;
    
    emit Withdraw(token, msg.sender, withdraw_amount, tokens[token][msg.sender]);
  }

  function balanceOfMyERC20(address token) constant public returns (uint) {
    require(authERC20Token(token) == true);
    return tokens[token][msg.sender];
  }

  function balanceOfUserERC20(address token, address user) constant public returns (uint) {
    require(authERC20Token(token) == true);
    return tokens[token][user];
  }

  function getTokenInfo(address _check_token) public view returns (string name, uint decimals) {
    
    require(authERC20Token(_check_token) == true);
    
    for (uint a = 0; a < numStokens; a++) {
       SupportedToken storage st = stokens[a];
        if (st.stokenAddr == _check_token) {break;}}
    
    name = st.stokenName;
    decimals = st.stokenDecimals;
  }
  
  // CORE SECTION OF THE CONTRACT:
  //
  // allocation functions 1 x ETH + 4 x ERC20

  // 1. Only the most foundational allocation function for ETH - Supports only the host ERC20
  function allocateETHbyHoldings(string _description) payable public {
    
    uint message_value = msg.value;
    uint management_cut = SafeMath.div(message_value,100) * management_fee;
    uint community_cut = SafeMath.div(message_value,100) * community_reward;
    uint remaining_value = message_value - management_cut - community_cut;
    uint total_holdings = calcTotalERC20Holdings(host_contract);

    // management_fee
    tokens[0][management_account] = SafeMath.add(tokens[0][management_account], management_cut);
    emit Deposit(0, management_account, management_cut, tokens[0][management_account]);

    // community_reward
    tokens[0][community_account] = SafeMath.add(tokens[0][community_account], community_cut);
    emit Deposit(0, community_account, community_cut, tokens[0][community_account]);

    // stakeholders' allocation    
    address[] memory owner_list = getDistinctOwners();
    uint share_a;
    uint amount_a;
    uint balance;
    address holder;
    
    for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        balance = ERC20Interface(host_contract).balanceOf(holder);
        share_a = calcul(balance, total_holdings, host_decimals);
        amount_a = SafeMath.div(remaining_value,10**host_decimals) * share_a; //
        tokens[0][holder] = SafeMath.add(tokens[0][holder], amount_a);
        emit Deposit(0, holder, amount_a, tokens[0][holder]);
    }
    addAEvent(0x0, msg.value, _description, 1);
  }

  // common functions to all ERC20 allocations
  function depositERC20allocation(address staker, address token, uint amount) internal {

   tokens[token][staker] = SafeMath.add(tokens[token][staker], amount);
    emit Deposit(token, staker, amount, tokens[token][staker]);
  }

  function prepAllocation(address _token, uint _amount) internal returns (uint remaining_value) {

    require (_token!=0);
    require (authERC20Token(_token) == true);
    require (ERC20Interface(_token).transferFrom(msg.sender, this, _amount));

    uint cut; // using the same variable twice
    remaining_value = _amount;

    // management_fee
    cut = SafeMath.div(_amount,100) * management_fee;
    depositERC20allocation(management_account, _token, cut);
    remaining_value = remaining_value - cut;

    // community_reward
    cut = SafeMath.div(_amount,100) * community_reward;
    depositERC20allocation(community_account, _token, cut);
    remaining_value = remaining_value - cut;
	}

  // 4. Allocates using distinct BOSS owners, i.e. only one slot for one address
  
  function allocateERC20byNFTs(address _token, uint _amount) public {
    
    uint remaining_value = prepAllocation(_token, _amount);
    uint total_supply = totalSupply();
    address[] memory owner_list = getDistinctOwners();
    uint share_a = calcul(1, total_supply, host_decimals);
    uint amount_a;
    address holder;
    
    for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        amount_a = SafeMath.div(remaining_value,10**host_decimals) * share_a;
        depositERC20allocation(holder, _token, amount_a);
    }
    addAEvent(_token, _amount, "Allocate ERC20 by BOSS NFTs", 4);
  }
  
  // 6. Allocates tokens by host erc721 contract external holdings like "Mutant Monsters ERC721Token"
  
  function allocateERC20byERC721Holdings(address _token, uint _amount, address _nft) public {

    require (authERC20Token(_token) == true);
    require (authERC721Token(_nft) == true);
    
    (string memory token_name, uint token_decimals) =  getTokenInfo(_token);
    //uint multipx = 10**token_decimals;

    // pre-requirements and management & community allocation
    uint remaining_value = prepAllocation(_token, _amount);
    
    // stakeholders' ERC721 shares
    // note: stakers % of ALL host721 NFTs
    uint total_erc721holdings = ERC721Interface(_nft).totalSupply();
    address[] memory owner_list = getDistinctOwners();
    uint share_a;
    uint amount_a;
    uint balance;
    address holder;
    
    for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        balance = ERC721Interface(_nft).balanceOf(holder);
        share_a = calcul(balance, total_erc721holdings, 0); // zero decimals for NFT holdings
        amount_a = SafeMath.div(remaining_value,10**token_decimals) * share_a;
        depositERC20allocation(holder, _token, amount_a);
    }
    
    addAEvent(_token, _amount, string(abi.encodePacked("Allocate ERC20 by ERC721 holdings using ", token_name, "and", _nft)), 6);
  }

  // 7. Allocates using total of staked balance of an ERC20 token
  function allocateERC20byBalance(address _deposittoken, uint _amount, address _check_token) public {
    
    (string memory token_name, uint token_decimals) =  getTokenInfo(_check_token);
    //uint multipx = 10**token_decimals;

    uint remaining_value = prepAllocation(_deposittoken, _amount);
    uint total_deposits = calcTotalERC20Deposits(_check_token);
    address[] memory owner_list = getDistinctOwners();
    uint share_a;
    uint amount_a;
    uint balance;
    address holder;
    
    for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        balance = tokens[_check_token][holder];
        share_a = calcul(balance, total_deposits, token_decimals);
        amount_a = SafeMath.div(remaining_value,10**token_decimals) * share_a;
        depositERC20allocation(holder, _deposittoken, amount_a);
    }
    
    string memory descr = string(abi.encodePacked("Allocate ERC20 by balance using ", token_name));
    addAEvent(_deposittoken, _amount, descr, 7);
  }
  
  // remember to call Token(address).approve(this, amount)
  // 8. Allocates tokens based by ERC20 external holdings  - Supports all ERC20s
  function allocateERC20byHoldings(address _deposittoken, uint _amount, address _check_token) public {
    
    (string memory token_name, uint token_decimals) =  getTokenInfo(_check_token);

    // pre-requirements and management & community allocation
    uint remaining_value = prepAllocation(_deposittoken, _amount);
    
    // stakeholders' shares
    uint total_holdings = calcTotalERC20Holdings(_check_token);
    address[] memory owner_list = getDistinctOwners();
    uint share_a;
    uint amount_a;
    uint balance;
    address holder;
    
    for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        balance = ERC20Interface(_check_token).balanceOf(holder);
        share_a = calcul(balance, total_holdings, token_decimals);
        amount_a = SafeMath.div(remaining_value,10**token_decimals) * share_a;
        depositERC20allocation(holder, _deposittoken, amount_a);
    }
    
    string memory descr = string(abi.encodePacked("Allocate ERC20 by holdings using ", token_name));
    addAEvent(_deposittoken, _amount, descr, 8);
  }

  // 9. Allocates using totals i.e. internal + external balance of an ERC20 token
  function allocateERC20byTotals(address _deposittoken, uint _amount, address _check_token) public {
      
    (string memory token_name, uint token_decimals) =  getTokenInfo(_check_token);
    
    uint remaining_value = prepAllocation(_deposittoken, _amount);
    uint total_totals = calcTotalERC20Holdings(_check_token) + calcTotalERC20Deposits(_check_token);
    address[] memory owner_list = getDistinctOwners();
    uint share_a;
    uint amount_a;
    uint total_balance;
    address holder;

    for (uint a = 0; a < owner_list.length; a++) {
        holder = owner_list[a];
        total_balance = ERC20Interface(_check_token).balanceOf(holder) + tokens[_check_token][holder];
        share_a = calcul(total_balance, total_totals, token_decimals);
        amount_a = SafeMath.div(remaining_value,10**token_decimals) * share_a;
        depositERC20allocation(holder, _deposittoken, amount_a);
    }

    string memory descr = string(abi.encodePacked("Allocate ERC20 by totals using ", token_name));
    addAEvent(_deposittoken, _amount, descr, 9);
  }  
  
}
