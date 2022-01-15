# Building your own Staking Contract
*Quest by Ravindra Kahar*

In this quest, you will learn all about Staking in Ethereum. You will learn about how staking in Ethereum works and how to build your own staking contract. Finally you will also learn about what are the requirements to become a validator and how you can earn rewards for staking your coins as a validator. And finally I will also share with you about how you can get started with Staking and earn rewards😎.  
  
Sounds interesting? Let’s get started...

## Gearing Up
I need you guys to follow along with me and write code yourself step by step and feel proud of it. Feel free to see my code for reference. Open Remix IDE in your browser and copy this boilerplate code so that we can quickly start building.

```

pragma solidity >=0.7.0; 

contract Storage {
    function isActive() public view returns(bool) {
    }
    function getContractBalance() public view returns(uint){
    }
    function deposit() public payable {  
    }
    receive() external payable { deposit(); }
    function withdraw() public {
    }
}
```
But before building our own staking smart contract, let’s first understand a few terms about Staking in Ethereum.

Ethereum staking:  is the process of locking up some amount of Ethers for a specified period of time in order to contribute to the security of the blockchain and earn rewards. People who do this are known as “stakers”. Ethereum will be operating on what is called a Proof of Stake algorithm to maintain the security. Though there are a lot of nuances to the protocol itself, all that you need to know is that Proof of Stake involves thousands of computers saying “I’m telling you the truth, if you can prove me wrong you can take way the 1000s of dollars that I have staked”. 

You can think of a blockchain as a form of a database anyone can write to and anyone can read from. There are thousands of computers maintaining the sanctity of this database. They do so by voting on what gets written and what gets read from this database. It relies on the fact that atleast half of the computers running this validation are going to be voting for the truth. If any of the computers are provably detected to be acting maliciously, they are punished. So to participate in this voting process a computer or node locks up some money. This money is lost when they misbehave. But, the voters/stakers get rewards for behaving rightfully. 

But why would someone like you be willing to be a staker anyway and why staking is important?
Stakers like you will be responsible for processing transactions on the Ethereum network and in turn receive rewards. Oftentimes, a validator in a PoS system will increase their chances of earning rewards on the network by staking more coins.
Conclusion : Stake more coins = Earn more rewards 💸

## Subquest : Coding the Staking Contract

We will be using 3 functions in our contract.  
We will use isActive() to check if our contract is active for staking, getContractBalance() to get total contract balance, deposit() to deposit some ethers (coins you want to stake and hold) into the contract and withdraw() to withdraw the amount we staked earlier in our contract.

At first, let’s think about the variables that we need:  
`uint totalContractBalance = 0` to keep track of the total amount of coins in our contract.  
`mapping(address => uint) public balance` to update balance staked by each user.  
`uint constant public threshold = 0.003 * 10**18` We will use this as a threshold amount. Let’s take it as 0.003 ETH. Observe that we have multiplied our amount with 10^18 because Solidity only supports integer values and we need to convert ETH into wei, which is the smallest unit.

  
`uint public deadline = block.timestamp + 1 minutes` We also need to keep track of the deadline, the time by which the threshold amount should be gathered. Block.timestamp indicates the time at which contract was deployed and the deadline is \+1 minutes from the time of deployment. You may change the deadline as per your wish.

## Deposit coins to your contract
Now that we have all variables in place, we will start writing our function deposit so that we can stake coins. It is important to keep make sure that our contract receives funds when deposit function is executed.

```  
receive() external payable { deposit(); }  
function deposit() public payable {}

```

After the contract is ready to receive funds when we deposit coins, we need to increase the balance of the address by transaction amount as well as the contract balance.

The `receive()` is an inbuilt function that is triggered when ethers are sent to this contract directly. 

```

deposit() {

  balance[msg.sender] += msg.value;

  totalContractBalance += msg.value;

}

```

We use the context variables `msg.sender` and `msg.value` to retrieve the sender address and the transaction amount. These are variables set by Ethereum for every function call after appropriate validations & checks. 

  
We now complete our function isActive(). We check whether the current time is within the deadline and the balance of the contract is more than the threshold amount. If both these conditions are fulfilled, then our contract will be active.

```
function isActive() public view returns(bool){

        return block.timestamp <= deadline && totalContractBalance >= threshold;

}
```
  
Also, we will write a simple function  which you might have already guessed, that it returns the totalContractBalance.

```

function getContractBalance() public view returns(uint){

        return totalContractBalance;

    }

```

Now at this point, our contract has the following code.

```

pragma solidity >=0.7.0  uint) public balance;

    uint constant public threshold = 0.003 * 10**18;

    uint public deadline = block.timestamp + 1 minutes;

    function deposit() public payable {

        balance[msg.sender] += msg.value;

        totalContractBalance += msg.value;

    }

function isActive() public view returns(bool){

        	return block.timestamp = threshold;

    	}

function getContractBalance() public view returns(uint){

        return totalContractBalance;

    }

}

```

Now, if we try to compile and deploy our contract, we will be able to deposit coins into our contract. We can also check the current status of the contract whether it is currently active or not.

Try to deposit money by entering the amount value at the cell just above the deploy button. Now, try to check the balance of the contract using getContractBalance button provided.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/838a05e9-4c0a-4c57-aacb-aa1675fa62e7.jpg)

You will notice the amount has been reflected in our contract. But if we try to check if our contract stake state is active or not, you will notice that if your added balance is less than the threshold amount, status is false.

## Withdraw coins from your contract
Since after deposit comes withdrawal, we will now code the ability to withdraw coins.

Now, in order to withdraw amount, we need to fulfill certain conditions. You are eligible to withdraw your amount if : 

- The staking period has exceeded the deadline. In other words, the contract should not be in an active state.
-  You must have of course deposited some money in order to withdraw.

To do these checks in Solidity, we use require(). If the conditions are not met the whole transaction will revert.

```

require(block.timestamp > deadline, "deadline hasn't passed yet");

require(!isActive(), "Contract is active");

require(balance[msg.sender] > 0, "You haven't deposited");

```

As a next step, we should transfer funds to the user if the withdrawal function is called and then set the balance of the sender to zero. The intuitive way of writing this logic might be something like this:

uint amount = balance[msg.sender];

balance[msg.sender] = 0;

totalContractBalance -= amount;

We also subtract the amount from our contract balance. Finally we set the active status of our contract back to false if the current time exceeds the deadline mentioned above.

## 6 - Playing with your Contract
Now that we have finished setting up our contract, it’s time to test it out.

Our final contract now looks like this : 

```

pragma solidity >=0.7.0  uint) public balance;

    uint constant public threshold = 0.003 * 10**18;

    uint public deadline = block.timestamp + 1 minutes;

    function getContractBalance() public view returns(uint){

        return totalContractBalance;

    }

    function isActive() public view returns(bool){

        return block.timestamp = threshold;

    }

    

    function deposit() public payable {

        balance[msg.sender] += msg.value;

        totalContractBalance += msg.value;

    }

    

    receive() external payable { deposit(); }

    

    function withdraw() public {

        require(block.timestamp > deadline, "deadline hasn't passed yet");

        require(!isActive(), "Contract is active");

        require(balance[msg.sender] > 0, "You haven't deposited");

        

        uint amount = balance[msg.sender];

        balance[msg.sender] = 0;

        totalContractBalance -= amount;

        payable(msg.sender).transfer(amount);

    }

}

```

Let’s quickly Compile the contract and then click Deploy.

Now we have several buttons to interact with our contract. First let’s try to deposit 4 ethers into the contract. Enter the amount in the value input field and change the unit to ether. 

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/ef530c25-75c4-4548-adeb-1b8d4ff4bfcc.jpg)

Now try to check the contract balance. If the transaction was successful the amount will be reflected in the contract. Since, earlier we had set the threshold stake value to be 3 ethers and now we have deposited 4 ethers. Can you guess the current status of or contract whether it is true or false? Try to think and then check the active status of the contract by clicking on the isActive button. The output should show true since the contract amount is greater than the threshold and the time has not exceeded the set deadline.

This time, if we try to withdraw money from an active contract, you will get relevant error message. You can withdraw coins only after the set deadline.

## Conclusion
So what happened here just now? Let’s just recall the concept of staking. We stake/lock our coins for some amount of time in order to become validators. These validators then are responsible to validate and process transactions made on the Ethereum network and in return, these validators are rewarded on equal ratio to every validator in the pool. If the number of validators are less in the pool or if you have staked a higher amount of coins, then you will get a higher percentage of reward. 

How to get involved in Staking?

Those interested in staking on the new Ethereum network would have to set up a staking node by running Ethereum 1.0 and Ethereum 2.0 clients. Ethereum clients are just software that enables nodes to interact with the Ethereum network. Compatible software clients for staking nodes include:

 Prysm: It is a Go language variant of the Ethereum software. 

Nimbus: This is an Eth1 and Eth2 lightweight version written in the Nim programming language.

Teku: This is an enterprise-focused Eth2 software client written in Java. Lighthouse: This software client uses Rust programming language.

Lodestar: This software client was created by Chaincode Labs and uses JavaScript/ Typescript. 

As a minimum requirement, you’ll need to use a computer with enough memory space to download both Ethereum blockchains – the old and the new. Already, Ethereum 1.0 contains around 900 gigabytes of data, and it expands at a rate of about 1GB per day. Validators are also expected to keep their nodes connected to the blockchain 24/7. Therefore, a quality internet connection is a core criterion. After you install your validator software on your computer, the next step is to lock away a minimum of 32 ETH to the appropriate Ethereum staking contract address. We will be looking at the steps to register as a staker in the next quest. 

Congratulations on making it to the end. I hope you learnt something new and this quest was beneficial to you. 

Happy learning!
