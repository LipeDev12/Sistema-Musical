# 🎵 Sistema Musical v2 — SENAI

Aplicação web completa para gerenciamento de biblioteca musical, cadastro de artistas e controle de acesso de usuários, desenvolvida para o curso do **SENAI** sob orientação do **Professor Denis**.

---

## 📌 Visão Geral

O **Sistema Musical v2** é um sistema web baseado na arquitetura **CRUD** (Create, Read, Update, Delete) com controle de acesso, manipulação de arquivos de mídia, integração com APIs externas (YouTube) e segurança contra as principais ameaças web.

### Funcionalidades
- **Autenticação de Usuários:** Login com senha criptografada em `BCRYPT` e controle de acesso por sessão (`valida_sessao.php`).
- **Gerenciamento de Artistas:** Cadastro, edição, listagem e exclusão de artistas com upload e redimensionamento automático de fotos.
- **Gerenciamento de Músicas:** Cadastro de faixas vinculadas a artistas (relacionamento `1:N`), com upload da capa do álbum e inclusão de vídeos do YouTube.
- **Biblioteca Musical Interativa:** Filtros combinados em tempo real (busca por palavra-chave em múltiplos campos, filtro por artista e filtro por gênero).
- **Processamento de Imagens:** Utilização da biblioteca GD do PHP para redimensionar fotos para o padrão 300x300px mantendo a proporção.
- **Segurança da Informação:** Proteção integral contra **SQL Injection** (via Prepared Statements) e **Cross-Site Scripting (XSS)** (via `htmlspecialchars`).

---

## 🛠️ Tecnologias Utilizadas

- **PHP 8+**
  - Conexão relacional via `mysqli` (com UTF-8 `utf8mb4`).
  - Criptografia com `password_hash()` e `password_verify()`.
  - Manipulação de imagens via **GD Library** (`imagecreatetruecolor`, `imagecopyresampled`, `imagejpeg`/`imagepng`).
  - Extrator de parâmetros de URL via Regex (Expressões Regulares) para o YouTube.
- **MySQL / MariaDB**
  - Modelagem relacional utilizando integridade referencial (`FOREIGN KEY`).
- **HTML5 & CSS3**
  - Folha de estilo centralizada com Variáveis CSS (`:root`), Flexbox, CSS Grid Layout e Responsividade Media Queries.

---

## 📂 Estrutura de Arquivos do Projeto

```text
.
├── bd.sql                 # Script DDL/DML de criação do banco de dados e dados iniciais
├── conexao.php            # Script de conexão MySQLi e criação do diretório img/
├── valida_sessao.php      # Middleware de proteção contra acessos não autorizados
├── login.php              # Tela de autenticação e validação de credenciais
├── logout.php             # Encerramento seguro de sessão e destruição de cookies
├── index.php              # Dashboard / Menu principal da aplicação
├── cadastro_artista.php   # CRUD completo de artistas + upload de fotos
├── cadastro_musica.php    # CRUD completo de músicas + upload de capas + Regex YouTube
├── listagem_musicas.php   # Biblioteca musical com filtros combinados e busca
├── geraSenha.php          # Script utilitário em CLI para gerar hashes de senha
└── styles.css             # Estilização unificada e responsiva de todas as telas
```

### Detalhamento dos Arquivos

| Arquivo | Descrição Técnica |
| :--- | :--- |
| `bd.sql` | Define o banco `sistema_musical_v2` e as tabelas `usuarios`, `artistas` e `musicas`. Insere o usuário padrão `admin`. |
| `conexao.php` | Estabelece a conexão com o banco MySQL via `mysqli`, força o charset `utf8mb4` e garante a criação da pasta `img/`. |
| `valida_sessao.php` | Garante que o script `session_start()` seja executado e bloqueia visitantes sem `$_SESSION['usuario']`. |
| `login.php` | Processa o formulário via `POST`, consulta a tabela de usuários via Prepared Statement e valida o hash da senha. |
| `logout.php` | Esvazia `$_SESSION`, apaga o cookie `PHPSESSID` definindo tempo passado no navegador e executa `session_destroy()`. |
| `index.php` | Apresenta a interface de boas-vindas com cards de atalho para os módulos do sistema. |
| `cadastro_artista.php` | Gerencia o cadastro/edição de artistas. Contém a função `redimensionarESalvarImagem()` e impede a exclusão de artistas vinculados a músicas. |
| `cadastro_musica.php` | Gerencia a gravação de músicas. Utiliza a função `getYoutubeVideoId()` para extrair a chave do vídeo e apaga o arquivo de imagem (`unlink`) ao remover a música. |
| `listagem_musicas.php` | Constrói consultas SQL dinâmicas concatenadas com `AND` para filtragem simultânea de busca geral, artista e gênero. |
| `geraSenha.php` | Utilitário CLI para geração rápida de hashes usando `PASSWORD_BCRYPT`. |
| `styles.css` | Contém regras de reset, temas de cores, animações de hover, alertas, botões, tabelas e ajustes para telas móbile. |

---

## ⚙️ Instalação e Execução Local

### 1. Requisitos do Sistema
- Servidor Web (Apache/Nginx) com suporte ao **PHP 8.0+**.
- Extensão `php-gd` ativada no arquivo `php.ini`.
- Banco de Dados **MySQL** ou **MariaDB**.

### 2. Passo a Passo

1. **Clonar/Copiar o projeto:**
   Coloque a pasta do projeto no diretório do servidor web (ex: `htdocs` no XAMPP ou `www` no WAMP).

2. **Importar o Banco de Dados:**
   Execute o script `bd.sql` no phpMyAdmin ou no terminal MySQL:
   ```sql
   CREATE DATABASE sistema_musical_v2;
   USE sistema_musical_v2;
   SOURCE bd.sql;
   ```

3. **Ajustar as Credenciais de Conexão:**
   Se necessário, atualize as credenciais no arquivo `conexao.php`:
   ```php
   $servername = "localhost";
   $username   = "root";
   $password   = "Senai@118"; // Altere conforme sua senha local
   $dbname     = "sistema_musical_v2";
   ```

4. **Acessar a Aplicação:**
   Abra o navegador e acesse:
   ```text
   http://localhost/sistema_musical/login.php
   ```

---

## 🔑 Credenciais Padrão

- **Usuário:** `admin`
- **Senha:** `123`

---

## 🛡️ Práticas de Segurança Implementadas

1. **Prevenção de SQL Injection (SQLi):**
   Todas as operações de consulta, exclusão e inserção utilizam declarações preparadas (`prepare`, `bind_param`, `execute`).

2. **Prevenção de Cross-Site Scripting (XSS):**
   Entradas tratadas na exibição com `htmlspecialchars()`.

3. **Criptografia de Senhas:**
   Uso do algoritmo seguro **BCRYPT** nativo do PHP.

4. **Segurança no Upload de Arquivos:**
   Revalidação de tipo da imagem (`getimagesize`, extensão JPG/PNG), limite de tamanho (5 MB) e recriação física do arquivo via GD para eliminar scripts maliciosos embutidos.

5. **Navegação Segura em Links Externos:**
   Links direcionados ao YouTube utilizam `target="_blank"` acompanhados de `rel="noopener noreferrer"`.
