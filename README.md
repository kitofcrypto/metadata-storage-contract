# metadata-storage-contract
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MetadataStorage {
    struct Metadata {
        string name;
        string description;
        string ipfsHash;
        address owner;
    }

    mapping(uint256 => Metadata) public metadataRecords;
    uint256 public metadataCount;
    
    event MetadataAdded(uint256 indexed id, string name, string ipfsHash, address indexed owner);
    event MetadataUpdated(uint256 indexed id, string name, string ipfsHash);
    event MetadataDeleted(uint256 indexed id);

    function addMetadata(string memory _name, string memory _description, string memory _ipfsHash) public {
        metadataRecords[metadataCount] = Metadata({
            name: _name,
            description: _description,
            ipfsHash: _ipfsHash,
            owner: msg.sender
        });
        
        emit MetadataAdded(metadataCount, _name, _ipfsHash, msg.sender);
        metadataCount++;
    }
    
    function updateMetadata(uint256 _id, string memory _name, string memory _description, string memory _ipfsHash) public {
        require(metadataRecords[_id].owner == msg.sender, "Only owner can update metadata");
        metadataRecords[_id].name = _name;
        metadataRecords[_id].description = _description;
        metadataRecords[_id].ipfsHash = _ipfsHash;
        
        emit MetadataUpdated(_id, _name, _ipfsHash);
    }
    
    function deleteMetadata(uint256 _id) public {
        require(metadataRecords[_id].owner == msg.sender, "Only owner can delete metadata");
        delete metadataRecords[_id];
        
        emit MetadataDeleted(_id);
    }
    
    function getMetadata(uint256 _id) public view returns (Metadata memory) {
        require(bytes(metadataRecords[_id].name).length > 0, "Metadata not found");
        return metadataRecords[_id];
    }
}
