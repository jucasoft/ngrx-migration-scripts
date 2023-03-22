# refactoring ngrx action base-class to createAction

## utilizzo di jscodeshift
```ts

// Imposta il percorso del file sorgente da migrare
const filePath = '/path/to/source/file.ts';

// Importa jscodeshift e il plugin per la selezione dei nodi TypeScript
const j = require('jscodeshift');
require('ts-jest-plugin').install(j);

// Carica il codice sorgente
const source = fs.readFileSync(filePath, 'utf8');

// Usa jscodeshift per trasformare il codice sorgente
const transformedSource = j(source)
  // Seleziona tutte le classi che estendono Action
  .find(j.ClassDeclaration)
  .filter(path => (
    path.superClass &&
    path.superClass.name === 'Action' &&
    path.superClass.type === 'Identifier'
  ))
  // Sostituisce ogni classe con una createAction
  .replaceWith(path => {
    // Crea una nuova azione utilizzando createAction
    const createActionNode = j.callExpression(j.identifier('createAction'), [
      j.literal(path.id.name),
      j.arrowFunctionExpression([j.identifier('payload')], j.objectExpression([
        j.property(j.identifier('payload'), j.identifier('payload'))
      ]))
    ]);
    // Restituisce la nuova azione
    return createActionNode;
  })
  .toSource();

// Salva il codice sorgente migrato
fs.writeFileSync(filePath, transformedSource);

```


## utilizzo di TypeScript-Refactor


Prima di tutto, TypeScript-Refactor è uno strumento molto utile per automatizzare il processo di rifattorizzazione del codice TypeScript. Ci sono diverse opzioni disponibili per l'installazione, ma uno dei modi più semplici per farlo è tramite npm:

`npm install -g typescript-refactor`

Una volta installato, possiamo utilizzarlo per convertire le action ngrx da base-class a createAction. Ecco i passaggi:

Aprire il file contenente le action ngrx che si desidera convertire.

Identificare le action che estendono la classe base Action e copiarle in un nuovo file.

Aprire la riga di comando e navigare nella cartella contenente il nuovo file.

Eseguire il seguente comando per convertire le action da base-class a createAction:

css
Copy code
tsrefactor -p tsconfig.json --action-to-create-action=path/to/new/file.ts --from=Path/To/Source/File.ts
Dove tsconfig.json è il file di configurazione TypeScript, path/to/new/file.ts è il percorso del nuovo file in cui verranno scritte le createAction e Path/To/Source/File.ts è il percorso del file sorgente contenente le action che vogliamo convertire.

Verificare che le createAction siano state create correttamente nel nuovo file.

Sostituire le vecchie action con le nuove createAction nel codice sorgente.

Verificare che il codice funzioni correttamente e che tutti i test passino.

In sintesi, TypeScript-Refactor ci permette di convertire le action ngrx da base-class a createAction in modo automatico, risparmiando tempo e riducendo il rischio di errori umani.
