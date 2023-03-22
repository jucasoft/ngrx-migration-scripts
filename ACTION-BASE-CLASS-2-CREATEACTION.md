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

ACTION-BASE-CLASS-2-CREATEACTION.md