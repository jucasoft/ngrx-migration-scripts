# refactoring ngrx action base-class to createAction
## utilizza ts-morph


1. Installa la libreria ts-morph tramite npm: ```npm install ts-morph```



2. Carica il tuo file sorgente TypeScript che contiene le classi delle azioni. Ad esempio, supponiamo che il tuo file sorgente si chiami "actions.ts":

```typescript
import { Project, SyntaxKind } from "ts-morph";

const project = new Project();
const file = project.addSourceFileAtPath("actions.ts");
```

3. Trova tutte le classi delle azioni nel file sorgente:
```typescript
const actionClasses = file.getClasses().filter(cls => {
  return cls.getExtends() && cls.getExtends().getText() === "Action";
});
```
4. Per ogni classe delle azioni trovata, genera una nuova funzione createAction:
```typescript
actionClasses.forEach(cls => {
  const actionName = cls.getName();
  const createAction = `export const ${actionName} = createAction('[${actionName}]', props<${actionName}Props>());`;
  file.addFunction({
    name: actionName,
    parameters: [
      {
        name: "props",
        type: `${actionName}Props`,
      },
    ],
    returnType: `ReturnType<typeof ${actionName}>`,
    statements: writer => {
      writer.write(createAction);
    },
  });
});
```

5. Scrivi le modifiche nel file sorgente:

```typescript
file.saveSync();
```
