# Deploy your app to Arweave Name System (ArNS)

The [starter kit](https://github.com/katana-network/specialk) comes with support for hosting sites to [Arweave Name System (ArNS)](ar.io/arns).

## What is the Arweave Name System?
ArNS domains are smart domains by AR.IO Network that allows you to associate any Arweave transaction ID with a human readable domain that is accessible as a subdomain of any gateway in the AR.IO Network.

For example, the ArNS domains’ app itself is deployed to ArNS:

- arns.ar.io
- arns.arweave.net
- arns.permagate.io

Same app available on multiple gateways.

You can access the same permanent site deployment as a subdomain on any of the (currently) over 600 active gateways in the network. You can see the full list of active gateways in the [network dashboard](https://gateways.ar.io ).

This means you pay once to store your app and in return get free permanent hosting with unlimited availability and high censorship resistance given the globally distributed and independent nature of the gateway network.

The only constraint is that your site must be client side (similar to IPFS).

## Getting started
ArNS suport is provided via CLI tool called [permaweb-deploy](https://github.com/permaweb/permaweb-deploy) and a Github Workflow that will deploy your site to ArNS on each push to your main branch.

This setup handles uploading your build folder to Arweave using [Turbo](https://docs.ardrive.io/docs/turbo/what-is-turbo.html), creating a [manifest](https://docs.ar.io/concepts/manifests), and then updating your ArNS name to point to your new manifest.

It has built in support for EVM Wallet signatures so the wallet you're using for Katana should work fine.

Before the workflow will work you must fund your wallet to pay for uploads and purchase an ArNS name.

### Funding your wallet

Turbo has a friendly topup page [here](https://turbo-topup.com/). Head to this page, connect your wallet and topup with either via credit card or ETH (on mainent).

This will associate a credit balance with your wallet that can be used to fund the upload of your site data to Arweave and purchasing an ArNS name.

### Pruchase an Arweave Name System (ArNS) name.

Head to to [ArNS app](https://arns.app/#/?search=) to purchase a name.

The ArNS supports purchasing with fiat (credit card via stripe), turbo credits or [ARIO tokens](ar.io/token). ArNS names can be lease or bought permanently.

**NOTE** - You can use the same credit balance to pay for uploads and ArNS names. The wallet you use to payfor uploads most also be the owner of the ArNS name.

### Update the `deploy:arweave` command

In the starter kit's `package.json` is a command for deploying to ArNS using permaweb-deploy. 

```
deploy:arweave": "bun run build:all && permaweb-deploy --arns-name <ARNS_NAME>
```

Update this command with the ArNS name you purchased in the last step

```
deploy:arweave": "bun run build:all && permaweb-deploy --arns-name specialK
```

### Add your wallet private key as a Github Secret.

For permaweb deploy to work you must provide the private keys for your wallet.

1. Export your private keys
2. Store as Github secret with value `DEPLOY_KEY`

Follow this guide for how to [export your private keys from Metamask](https://support.metamask.io/configure/accounts/how-to-export-an-accounts-private-key/#:~:text=Click%20the%20three%20vertical%20dots,private%20key%20to%20your%20clipboard.).


### Push changes to your `main` branch

If you have follow the steps above correctly, everytime you push to your `main` branch, the github workflow with automatically:

1. Upload only the changes files to Arweave and return a TxID
2. Update your ArNS name to point to this TxID.

You will now be able to view your website at yourarnsname.ar.io (or any other gateway in the network).

Here is the latest version of the startkit deployed to ArNS - https://specialk.ar.io.

