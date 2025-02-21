# 🏆 Achievement NFT Smart Contract  

## 📜 Description  
The **Achievement NFT Smart Contract** allows users to **mint, store, and trade NFTs** as proof of their accomplishments. Each NFT includes **unique metadata**, ensuring verifiable digital ownership. Users can **transfer NFTs, track their collections, and check total minted NFTs**.  

## 🚀 Features  
- **Mint Achievement NFTs** with custom metadata.  
- **Immutable Ownership** recorded on the blockchain.  
- **Tradeable NFTs** – users can transfer ownership.  
- **Retrieve NFT Details** and track total minted NFTs.  
- **No Deployment Inputs Required**, making it easy to use.  

## 📂 Smart Contract Code  
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AchievementNFT {
    struct NFT {
        address owner;
        uint256 id;
        string metadata;
    }

    mapping(uint256 => NFT) public nfts;
    mapping(address => uint256[]) public ownerToNFTs;
    
    uint256 public totalNFTs;

    event NFTMinted(address indexed owner, uint256 indexed id, string metadata);

    function mintNFT(string memory _metadata) public {
        totalNFTs++;
        nfts[totalNFTs] = NFT(msg.sender, totalNFTs, _metadata);
        ownerToNFTs[msg.sender].push(totalNFTs);
        
        emit NFTMinted(msg.sender, totalNFTs, _metadata);
    }

    function getNFT(uint256 _id) public view returns (address, uint256, string memory) {
        require(nfts[_id].id != 0, "NFT does not exist");
        NFT memory nft = nfts[_id];
        return (nft.owner, nft.id, nft.metadata);
    }

    function getUserNFTs(address _user) public view returns (uint256[] memory) {
        return ownerToNFTs[_user];
    }

    function transferNFT(uint256 _id, address _to) public {
        require(nfts[_id].owner == msg.sender, "Not the owner");
        
        nfts[_id].owner = _to;
        ownerToNFTs[_to].push(_id);

        uint256[] storage userNFTs = ownerToNFTs[msg.sender];
        for (uint256 i = 0; i < userNFTs.length; i++) {
            if (userNFTs[i] == _id) {
                userNFTs[i] = userNFTs[userNFTs.length - 1];
                userNFTs.pop();
                break;
            }
        }
    }

    function totalMintedNFTs() public view returns (uint256) {
        return totalNFTs;
    }
}
