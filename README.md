# debug-vs-code - Como configurar o debug do VS Code com um container docker?

## Problema 
Você possui um repositório local em nodejs que roda em um container docker e precisa conectar seu debug no container para poder efetuar debug. 


## Passo 1
No seu package.json global, adicione --inspect=0.0.0.0:9229 como abaixo.
```
"scripts": {
    "start": "node --inspect=0.0.0.0:9229 --max-old-space-size=4096 ./node_modules/.bin/serverless offline start --stage local",
```

## Passo 2
No seu docker-compose.yml ou docker files, procure pela porta em que seu container executa e adicione a porta 9229, como abaixo, isso instrui seu container a expor essa porta, para que o VS Code se conecte ao container.
```
    ports:
      - '4000:4000'
      - '9229:9229'
```

## Passo 3
Execute o seguinte comando. Isso irá expor a porta do seu container para sua máquina. DISCLAIMER -> Passo 2 e 3 tecnicamente fazem a mesma coisa, mas você precisa executar ambos quando estiver usando docker-compose. Veja os links abaixo para mais detalhes.
```
docker run --rm -d -p 3000:3000 -p 9229:9229 -v ${PWD}:/usr/src/app -v /usr/src/app/node_modules nome_da_imagem
```

## Passo 4
No seu VS Code, clique no icone de debug, daí clique em "add-configuration (seu-container)", o VS Code vai abrir um arquivo chamado lauch.json, aqui é onde você descreve como seu VS Code deve se comportar em relacao ao debug em tempo de execução cucao do seu código. Copie e cole o seguinte arquivo no seu launch.json. Mais expicacoes sobre esse json estao nos links abaixo.

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "attach",
            "name": "Docker: Attach to Node",
            "port": 9229,
            "address": "localhost",
            "localRoot": "${workspaceFolder}",
            "remoteRoot": "/app",
            "protocol": "inspector",
            "restart": true,
            "sourceMaps": true,
            "skipFiles": [
                "${workspaceFolder}/node_modules/**/*.js",
                "${workspaceFolder}/.webpack/**/*.js",
                "${workspaceFolder}/lib/**/*.js",
                "<node_internals>/**/*.js",
              ],
            "outFiles": [
                "${workspaceFolder}/.webpack/**/*.js"
              ],
        }
    ]
}
```


## Passo 5
Inicie ou reinicie seu container. Clique no botão de Play verde, ainda na tela de debug do seu VS Code, no seu terminal, onde você  verifica os logs da aplicação, uma mensagem dizendo que um debugger se conectou aparecerá (O VS code informa isso a você  também). Marque alguns breakpoints e aproveite!!! Caso os breakpoints nao fiquem marcados, basta você fazer alguma tarefa no seu código, uma chamada de api, por exemplo. Ainda estou aprendendo os comportamentos do debug. PR's sao bem vindos.



## Sugestoes de leitura e aulas.
- https://dev.to/alex_barashkov/how-to-debug-nodejs-in-a-docker-container-bhi
- https://code.visualstudio.com/docs/remote/containers
- https://www.youtube.com/watch?v=AX7uybwukkk
- https://www.youtube.com/watch?v=x4q86IjJFag
- https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_skipping-uninteresting-code-node-chrome
