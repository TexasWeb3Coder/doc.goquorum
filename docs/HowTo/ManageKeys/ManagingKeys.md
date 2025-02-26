# Managing keys

Options for managing keys in GoQuorum include:

* [`keystore` files](https://geth.ethereum.org/docs/interface/managing-your-accounts)

    As with geth, keys can be stored in password-protected `keystore` files.

* [`clef`](clef.md)

    Introduced in GoQuorum v2.6.0, `clef` runs as a standalone process that increases flexibility and security by handling GoQuorum's account management responsibilities.

* [`account` plugins](AccountPlugins.md)

    Introduced in GoQuorum v2.7.0, `account` plugins allow GoQuorum or `clef` to be extended with alternative methods of managing accounts.
