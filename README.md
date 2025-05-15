# PumpFunSDK README
fork by https://github.com/rckprtr/pumpdotfun-sdk.git
Key Update: pumpfun upgrade

## Important

Never click links in this repository leaving github, never click links in Issues, don't run code that others post without reading it, this software is provided "as is," without warranty.

## Overview

The `PumpDotFunSDK` is designed to interact with the Pump.fun decentralized application. It provides methods for creating, buying, and selling tokens using the Solana blockchain. The SDK handles the necessary transactions and interactions with the Pump.fun program.

## Installation

`
npm i @jwenlee/pumpdotfun-sdk
`

## Reference
```typescript
    const mint = Keypair.generate();
    console.log("Generated address:", mint.publicKey.toBase58());

    const provider = getProvider();
    const sdk = new PumpFunSDK(provider);

    const imageResponse = await fetch(imageUrl);
    // console.log("imageResponse", imageResponse);

    const arrayBuffer = await imageResponse.arrayBuffer();
    const imageFile = new File([Buffer.from(arrayBuffer)], "token-image.jpg", {
      type: "image/jpeg"
    });
    console.log("imageFile", imageFile);

    const tokenMetadata: CreateTokenMetadata = {
      name,
      symbol: ticket,
      description,
      file: imageFile,
      ...optionalParams
    };
    const metadata = await sdk.createTokenMetadata(tokenMetadata);
    console.log("metadata", metadata);

    let createTx = await sdk.getCreateInstructions(
      creatorAddress,
      String(tokenMetadata.name),
      String(tokenMetadata.symbol),
      metadata.metadataUri,
      mint
    );

    // console.log("createTx", createTx);
    let tx = new Transaction();
    tx.add(createTx);

    if (buyAmountSol > 0) {
      const globalAccount = await sdk.getGlobalAccount("finalized");
      const buyAmount = globalAccount.getInitialBuyPrice(buyAmountSol);
      const buyAmountWithSlippage = calculateWithSlippageBuy(
        buyAmountSol,
        slippageBasisPoints
      );
      // @ts-ignore
      const buyTx = await sdk.getBuyInstructions(
        creatorAddress,
        mint.publicKey,
        globalAccount.feeRecipient,
        buyAmount,
        buyAmountWithSlippage,
        "confirmed",
        true,
        creatorAddress
      );
      tx.add(buyTx);
    }
```