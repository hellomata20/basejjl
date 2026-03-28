# basejjlimport time
from web3 import Web3

RPC_URL = "https://mainnet.base.org"


def main():
    w3 = Web3(Web3.HTTPProvider(RPC_URL))

    if not w3.is_connected():
        raise RuntimeError("Cannot connect to Base RPC")

    print("Connected to Base")
    print("Calculating average transaction value per block...\n")

    last_block = w3.eth.block_number

    while True:
        try:
            current_block = w3.eth.block_number

            if current_block > last_block:

                for block_num in range(last_block + 1, current_block + 1):

                    block = w3.eth.get_block(block_num, full_transactions=True)

                    tx_count = len(block.transactions)

                    if tx_count == 0:
                        continue

                    total_value = sum(tx.value for tx in block.transactions)
                    avg_value = total_value / tx_count

                    avg_eth = w3.from_wei(avg_value, "ether")

                    print("Block:", block_num)
                    print("Transactions:", tx_count)
                    print("Average tx value:", avg_eth, "ETH")
                    print()

                last_block = current_block

            time.sleep(2)

        except Exception as e:
            print("Error:", e)
            time.sleep(5)


if __name__ == "__main__":
    main()
