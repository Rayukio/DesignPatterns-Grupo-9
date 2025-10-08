# DesignPatterns-Grupo-9
## Introdução ao tema (Design Patterns)

  Os padrões de projetos (Design Patterns) são soluções reutilizáveis e testadas para problemas recorrentes no design de software. Não são códigos prontos, mas servem de guia de estruturas, ou seja, descrevem classes, responsabilidades e interações entre componentes para resolver problemas comuns de projetos.
  
  As principais categorias são as criacionais, estruturais e comportamentais. Os padrões criacionais, tratam da criação de objetos, desacoplando a lógica de criação da lógica de uso, com isso, aumenta sua flexibilidade e reutilização. Já os padrões estruturais tratam da composição de classes e objetos para a formação de estruturas maiores, ainda assim, mantendo a flexibilidade e a eficiencia da estrutura. Por fim, os padrões comportamentais, tratam da comunicação e responsabilides entre os objetos.

## Descrição do(s) padrão(ões) escolhido(s)
  O Singleton é um padrão de projeto criacional que provê uma unica instância de uma determinada classe durante toda a execução da aplicação. Com isso, se tem o controle do acesso à um recurso compartilhado, como uma base de dados ou arquivos, portanto, no caso da nossa aplicação, se aplica ao banco de dados da nossa solução.

  As vantagens então são: evita múltiplas conexões desnecessárias com o banco, centraliza a lógica de conexão e para alterar credenciais ou configurações, só é necessário alterar em um só lugar. Porém temos como trade-offs: dificultar testes unitários se não for bem isolado e introduz dependência global.

## Estrutura de classes ou diagrama UML representando a implementação

<img width="211" height="124" alt="Diagrama sem nome-Página-1 drawio" src="https://github.com/user-attachments/assets/5202272d-515e-48bc-bff3-c67f03dc2827" />

## Trechos de código ilustrativos
O código abaixo é um exemplo do singleton aplicado a um banco de dados:

    const { Pool } = require('pg');

    class DatabaseSingleton {
      static instance = null;

      constructor() {
        if (DatabaseSingleton.instance) {
          return DatabaseSingleton.instance;
        }

        this.client = new Pool({
          host: 'localhost',
          port: 5432,
          user: 'postgres',
          password: 'senha',
          database: 'ifix_db'
        });

        DatabaseSingleton.instance = this;
      }

      static getInstance() {
        if (!DatabaseSingleton.instance) {
          DatabaseSingleton.instance = new DatabaseSingleton();
        }
        return DatabaseSingleton.instance;
      }

      getClient() {
    return this.client;
      }
    }

    module.exports = DatabaseSingleton;


O index para esse caso:

    const express = require('express');
    const DatabaseSingleton = require('./database/DatabaseSingleton');

    const app = express();
    const port = 3000;

    app.get('/usuarios', async (req, res) => {
      try {
        const db = DatabaseSingleton.getInstance().getClient();
        const result = await db.query('SELECT * FROM usuarios');
        res.json(result.rows);
      } catch (err) {
        console.error('Erro ao consultar usuários:', err);
        res.status(500).json({ error: 'Erro interno do servidor' });
      }
    });

    app.listen(port, () => {
      console.log(`Servidor rodando na porta ${port}`);
    });


    module.exports = DatabaseSingleton;

## Instruções de execução e/ou testes
Como executar:

    npm install
    node src/index.js

O código abaixo mostra um teste manual simples:

    const DatabaseSingleton = require('../database/DatabaseSingleton');

    const db1 = DatabaseSingleton.getInstance();
    const db2 = DatabaseSingleton.getInstance();

    console.log('Mesma instância?', db1 === db2);  // Deve imprimir: true


## Conclusões e aprendizados do grupo
  Os padrões de projetos são bem importantes pois são como guias para solucionar problemas de projeto reco
