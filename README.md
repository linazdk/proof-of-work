### Blockchain.js

* **newBlock** (proof, previousHash) => block
  Generate a new block given the already calculated proof and the previous Hash and returns the obtained block
* **newTransaction** (sender, receiver, amount) => transaction
  Generate a new transaction and adds it to the currentTransactions array which will be added in the next new block
* **mine** (miner) => block
  Mines a new block, assigns it to the miner and generate the associated block

### Network.js

* **registerNode** (address) => boolean
  Enable the insertion of new nodes and returns if the address has been added (cannot add twice the same address)
* **nodeExists** (address) => boolean
  Fetches in the nodes array if the provided node already exists and returns this information

## Démonstration
Vidéo montrant la validation de la blockchain avec `/checkChain` :
https://drive.google.com/file/d/1Hjk9rCEo3VGg4HlVwvHC6JMRdeFL9Qg_/view?usp=sharing

## Analyse Critique du Proof of Work

### Problèmes de l'implémentation actuelle
L'implémentation actuelle du Proof of Work présente des failles de sécurité :
1. **Faible difficulté** : Le calcul de preuve est une simple multiplication, ce qui est trivialement facile à falsifier
2. **Pas d'usage de hash cryptographique** : Il n'y a pas d'utilisation de SHA-256 ou d'un autre algorithme sécurisé
3. **Risque d'attaque Sybil et 51%** : Un attaquant peut miner des blocs trop facilement et contrôler la blockchain
4. **Pas d'ajustement dynamique de la difficulté** : Le minage n'est pas ralenti, donc un attaquant peut créer des blocs à volonté

### Risques de Sécurité
- **Double spending** : Un mineur malveillant pourrait recalculer une preuve et modifier des transactions
- **Faible sécurité cryptographique** : La blockchain ne repose pas sur un calcul suffisamment difficile
- **Attaque Sybil** : Un attaquant peut générer plusieurs identités et miner sans contrainte

### Améliorations Proposées
Pour sécuriser le Proof of Work :
1. **Utiliser SHA-256 pour sécuriser la preuve**
2. **Exiger que le hash commence par plusieurs `0`** (`0000abcd...`)
3. **Implémenter un nonce et une vérification de difficulté**
4. **Dynamiser la difficulté** pour empêcher un minage trop rapide

### Nouvelle Implémentation Sécurisée
Voici une implémentation améliorée du Proof of Work :

```javascript
const crypto = require('crypto');

function generateProof(lastProof){
    let proof = 0;
    while(!isValidProof(lastProof, proof)){
        proof++;
    }
    return proof;
}

function isValidProof(lastProof, proof){
    const guess = `${lastProof}${proof}`;
    const guessHash = crypto.createHash('sha256').update(guess).digest('hex');
    return guessHash.startsWith('0000'); 
}

