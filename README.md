# Evidence
Timestamping as a service (TaaS)
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract EVIDENCE {
    struct Document {
        bytes32 hash;
        uint256 timestamp;
    }

    mapping(address => Document[]) private userDocuments;

    event DocumentTimestamped(address indexed user, bytes32 indexed documentHash, uint256 timestamp);

    function generateDocumentHash(bytes memory document) public pure returns (bytes32) {
        return sha256(document);
    }

    function submitDocument(bytes memory document) public {
        bytes32 documentHash = generateDocumentHash(document);
        userDocuments[msg.sender].push(Document(documentHash, block.timestamp));

        emit DocumentTimestamped(msg.sender, documentHash, block.timestamp);
    }

    function getDocumentCount() public view returns (uint256) {
        return userDocuments[msg.sender].length;
    }

    function getDocumentByIndex(uint256 index) public view returns (bytes32, uint256) {
        require(index < userDocuments[msg.sender].length, "Invalid index");
        Document memory document = userDocuments[msg.sender][index];
        return (document.hash, document.timestamp);
    }

    function verifyTimestamp(bytes memory document, uint256 timestamp) public view returns (bool) {
        bytes32 documentHash = generateDocumentHash(document);
        for (uint256 i = 0; i < userDocuments[msg.sender].length; i++) {
            Document memory doc = userDocuments[msg.sender][i];
            if (doc.hash == documentHash && doc.timestamp == timestamp) {
                return true;
            }
        }
        return false;
    }
}
