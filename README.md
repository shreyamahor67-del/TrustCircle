// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

/**
 * @title TRUEST CIRCLE
 * @dev A basic smart contract for creating trust-based circles of verified members
 */
contract TruestCircle {
    address public admin;

    // Circle members
    mapping(address => bool) public isMember;
    uint public memberCount;

    // Events
    event MemberAdded(address indexed newMember);
    event MemberRemoved(address indexed removedMember);

    constructor() {
        admin = msg.sender;
        isMember[admin] = true;
        memberCount = 1;
    }

    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can perform this action");
        _;
    }

    modifier onlyMember() {
        require(isMember[msg.sender], "Only circle members can perform this action");
        _;
    }

    /// @notice Add a new member to the circle
    function addMember(address _newMember) external onlyAdmin {
        require(!isMember[_newMember], "Already a member");
        isMember[_newMember] = true;
        memberCount += 1;
        emit MemberAdded(_newMember);
    }

    /// @notice Remove a member from the circle
    function removeMember(address _member) external onlyAdmin {
        require(isMember[_member], "Not a member");
        require(_member != admin, "Cannot remove admin");
        isMember[_member] = false;
        memberCount -= 1;
        emit MemberRemoved(_member);
    }

    /// @notice Verify if an address is a trusted member
    function isTrusted(address _addr) external view returns (bool) {
        return isMember[_addr];
    }
}
