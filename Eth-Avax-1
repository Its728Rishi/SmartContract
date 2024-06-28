// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VoterIDSystem {
    struct Voter {
        uint256 id;
        string name;
        bool isRegistered;
    }

    mapping(address => Voter) public voters;
    uint256 public totalVoters;
    address public admin;

    constructor() {
        admin = msg.sender; // The person deploying the contract becomes the admin
    }

    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can call this function");
        _;
    }

    event VoterRegistered(address voterAddress, uint256 voterId, string name);
    event VoterVerified(address voterAddress, uint256 voterId);

    function registerVoter(address _voterAddress, uint256 _voterId, string memory _name) public onlyAdmin {
        require(!voters[_voterAddress].isRegistered, "Voter is already registered");

        voters[_voterAddress] = Voter({
            id: _voterId,
            name: _name,
            isRegistered: true
        });

        totalVoters++;
        
        emit VoterRegistered(_voterAddress, _voterId, _name);
    }

    function verifyVoter(address _voterAddress) public view returns (bool) {
        Voter memory voter = voters[_voterAddress];
        require(voter.isRegistered, "Voter is not registered");
        assert(voter.id != 0); // Ensures that the voter ID is valid
        return voter.isRegistered;
    }

    function updateVoterName(address _voterAddress, string memory _newName) public onlyAdmin {
        Voter storage voter = voters[_voterAddress];
        require(voter.isRegistered, "Voter is not registered");

        voter.name = _newName;
    }

    function removeVoter(address _voterAddress) public onlyAdmin {
        Voter storage voter = voters[_voterAddress];
        require(voter.isRegistered, "Voter is not registered");

        delete voters[_voterAddress];
        totalVoters--;

        // Reverting if the voter was not successfully removed (example of revert usage)
        if (voters[_voterAddress].isRegistered) {
            revert("Failed to remove voter");
        }
    }
}
