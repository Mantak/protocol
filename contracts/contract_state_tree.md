[back](./contracts.md)
## Contract state tree

The contracts reside in a contract state tree that
is included in the root hash. The existence of a contract can be proven.
Interactions, i.e. calls and return values, are stored in their own
state tree for one block. The existence of the call in that block can be proven.

### Contract state tree objects

The contract state tree contains objects:
- The contract definition (code binary)
- The contract store (data binary)
- The contract log (data binary)
- Active flag (Boolean)
- List of referring contracts (Note this will be changed to a list of referred contracts and a refcount.)
- Deposit (amount)
- vm_version

See [Contract Serialization](../serializations.md#contract) for the serialization specification.

The contract also has a nonce and a balance recorded in an account state tree.

A call to a contract can execute chain transactions and these transactions are allowed to spend
tokens from the balance of the contract.

#### The contract definition

- Created by a contract create transaction.
- Deleted when an inactive contract has no more referrers.

### Contract state tree update

The contract's account state tree is updated:
- When the balance of a contract account is changed
  - Through a spend to the contract.
  - Through a call to the contract, with the transferred amount of the call.
  - Through a spend from a known account with an attached contract.
- When a contract's balance is updated by a call executing a spend transaction.
- When a call to a contract executes a transaction (create contract, call contract, spend,
  etc.) the nonce of the contract is increased by one.
  (This is subject to change in future versions.)

The contract's state tree is updated:
- Upon success of a contract call the contract log and the contract's storage sub tree is updated.
- When another contract is created and that new contract refers to the contract in the code, the refcount is increased.
- When another contract that refers to the contract is deleted the refcount is decreased.
- When the contract executes the deactivate instruction the active field is set to false.

The fields owner, vm_version, code, and deposit are static throughout a contract's life time.

### Pruning of contracts

TO BE WRITTEN.

## Calls state tree

The calls state tree contains all calls done in the block and their
return values. The calls state tree is pruned in each block so that
the latest block only contains the calls in that block.

See [Call Serialization](../serializations.md#contract-call) for the serialization specification.
