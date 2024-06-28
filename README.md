VoterIDSystem

Overview

.The VoterIDSystem is a smart contract written in Solidity for Ethereum blockchain. This contract manages a simple voter ID system where an admin can register, verify, update, and remove voters. The contract includes basic security features such as restricting certain functions to only the admin.

Features

.Admin Control: Only the admin (the account that deployed the contract) can register, update, or remove voters. 

.Voter Registration: The admin can register voters by providing a unique ID, name, and address.

.Voter Verification: Anyone can verify if a voter is registered.

.Voter Name Update: The admin can update the name of a registered voter.

.Voter Removal: The admin can remove a registered voter from the system.

Contract Structure

State Variables

.mapping(address => Voter) public voters: Maps each voter's address to their voter details.

.uint256 public totalVoters: Keeps track of the total number of registered voters.

.address public admin: Stores the address of the admin who deployed the contract.

Structs

.struct Voter: Represents a voter with an ID, name, and registration status.

Modifiers

.onlyAdmin(): Restricts function access to the admin only.

Events

.event VoterRegistered(address voterAddress, uint256 voterId, string name): Emitted when a voter is registered.

.event VoterVerified(address voterAddress, uint256 voterId): Emitted when a voter is verified.

Functions

.constructor(): Initializes the contract and sets the deployer as the admin.

.registerVoter(address _voterAddress, uint256 _voterId, string memory _name) public onlyAdmin: Registers a new voter.

.verifyVoter(address _voterAddress) public view returns (bool): Verifies if a voter is registered.

.updateVoterName(address _voterAddress, string memory _newName) public onlyAdmin: Updates the name of a registered voter.

.removeVoter(address _voterAddress) public onlyAdmin: Removes a registered voter.

Usage

Deployment

.To deploy the contract, use a suitable Ethereum development environment like Remix or Truffle. Ensure you have an Ethereum wallet with some test ETH for gas fees.

Interacting with the Contract

Register a Voter

.Call registerVoter(address _voterAddress, uint256 _voterId, string memory _name) with the voter's address, ID, and name.

Verify a Voter

.Call verifyVoter(address _voterAddress) with the voter's address. This will return true if the voter is registered.

Update Voter Name

.Call updateVoterName(address _voterAddress, string memory _newName) with the voter's address and the new name.

Remove a Voter

.Call removeVoter(address _voterAddress) with the voter's address to remove them from the registry.

Example

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
    
License

.This project is licensed under the MIT License - see the LICENSE file for details.
