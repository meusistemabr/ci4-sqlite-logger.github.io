---
layout: default
title: Codeigniter SQLite Logger - Sistema de Logging forense
nav_order: 1
description: "Documentação oficial do CI4 SQLite Logger. Sistema de logging forense rotativo em SQLite para CodeIgniter 4 com hash chain SHA-256 e auditoria estruturada."
permalink: /
---

# CI4 SQLite Logger 🛡️
{: .no_toc .fw-700 .text-center }


Biblioteca de alta integridade e auditoria estruturada para o framework [CodeIgniter 4](https://codeigniter.com/ "CodeIgniter Official Site"). Projetada para substituir arquivos de texto planos (`.log`) por bancos relacionais [SQLite](https://www.sqlite.org/ "SQLite Database Engine") rotativos criptograficamente encadeados.

Ao desenvolver esta biblioteca e conceber seu funcionamento, dedicamos anos de estudo e aplicamos os conhecimentos adquiridos em nossa prática profissional.  Quando a equipe de programação da Meu Sistema inicia qualquer projeto, submetemos-nos a um `extenso e rigoroso processo de ambientação`.  Todos os engenheiros e projetistas participam de treinamentos presenciais, testes de frameworks, discussões e diversas reuniões de alinhamento, garantindo que todos os envolvidos no projeto compreendam seu funcionamento integral.

Ao embarcarmos em mais um projeto com a assinatura da Meu Sistema, nos deparamos com a necessidade premente de garantir rastreamento, confiabilidade e uma cadeia de custódia robusta. No âmbito da perícia digital, a execução completa do processo de cadeia de custódia e a obtenção de provas digitais que sejam totalmente confiáveis e impugnáveis pela defesa adversária representam um desafio significativo.  Este desafio não reside apenas na expertise do perito, mas também na qualidade e confiabilidade dos equipamentos utilizados, do software empregado, das APIs integradas e dos logs gerados durante o processo. Cada um desses elementos desempenha um papel crucial na construção de um caso digital sólido e defensável.  A Meu Sistema se compromete a fornecer as ferramentas e o suporte necessários para que nossos peritos possam superar esses desafios com sucesso, garantindo a integridade e a confiabilidade das provas digitais apresentadas.

Após uma extensa e minuciosa pesquisa, constatamos que não existe nenhuma biblioteca de código aberto disponível no mercado que atinja o nível de segurança excepcional proporcionado por esta biblioteca.  Essa biblioteca se destaca por sua capacidade de atingir um alto grau de segurança utilizando componentes pequenos e simples, sem a necessidade de hardware sofisticado ou de alto desempenho.  Diante desse cenário, e em comemoração à entrega bem-sucedida do projeto, fizemos uma promessa ao nosso cliente: desenvolveríamos uma biblioteca que não apenas atendesse às suas necessidades específicas, mas também demonstrássemos, de forma transparente e ao vivo, todo o processo de segurança em ação.  Essa demonstração ao vivo visa proporcionar ao cliente `total confiança na eficácia e robustez da biblioteca`, garantindo que suas informações estejam protegidas com o mais alto nível de segurança disponível.  Nosso compromisso é com a excelência e a satisfação do cliente, e estamos dedicados a entregar uma solução que supere suas expectativas e atenda às suas demandas mais exigentes.

Desenvolvemos a biblioteca "CI4 SQLite Logger", a qual foi prontamente disponibilizada no GitHub para a comunidade de desenvolvedores.  Ao longo do tempo, dedicamos esforços contínuos para aprimorar e adaptar a biblioteca, garantindo sua robustez e confiabilidade.  Com o objetivo de facilitar ainda mais a integração e o uso da biblioteca, assim que alcançamos uma versão totalmente confiável e auditável, publicamos-na no Composer, um gerenciador de pacotes amplamente utilizado no ecossistema [PHP](https://www.php.net/ "Popular general-purpose scripting language").  Simultaneamente, tornamos o repositório da biblioteca público, marcando o início oficial de sua utilização e adoção pela comunidade.  Após a implementação bem-sucedida da biblioteca em nosso próprio caso de uso, sentimos a responsabilidade de demonstrar a eficácia dos processos de auditoria que ela proporciona.  Em uma demonstração prática, explicamos detalhadamente que a utilização desta biblioteca garante a integridade dos registros armazenados, tornando impossível a alteração de qualquer dado sem a quebra da cadeia de hashes.  Este mecanismo assegura que qualquer modificação nos registros seja detectada imediatamente, **proporcionando um alto nível de segurança e confiabilidade para aplicações que dependem de auditoria rigorosa e rastreabilidade de dados**.

O script, em sua implementação inicial, executa a criação de um nó de hash criptográfico robusto, utilizando para isso APIs de segurança do sistema operacional (SO) ou bytes seguros aleatórios.  Essa operação fundamental estabelece o primeiro elo da corrente de custódia, uma estrutura criptográfica projetada para garantir a integridade e a segurança dos dados armazenados. Uma vez estabelecido, este elo inicial torna-se inalterável e inquebrável, constituindo a base sólida sobre a qual toda a cadeia de custódia é construída.  A integridade desta corrente é de suma importância, pois qualquer quebra ou corrupção do elo inicial compromete a segurança e a confiabilidade de toda a cadeia.  

Consequentemente, uma cadeia de nós que tenha sofrido tal quebra ou corrupção torna-se inútil para fins de auditoria ou comprovação de provas, visto que sua funcionalidade e segurança foram comprometidas.  Portanto, a criação do nó inicial com o máximo rigor e segurança é essencial para garantir a eficácia e a confiabilidade da cadeia de custódia como um todo.
{: .note }

---

## Por que trocar logs em texto por SQLite?

Arquivos tradicionais de log em texto puro (`.log`) apresentam vulnerabilidades graves de segurança e limitações de escala em ambientes de produção:

* **Adulteração Silenciosa:** Se um atacante obtiver privilégios no servidor, linhas de log podem ser apagadas ou alteradas sem deixar vestígios.
* **Leitura Ineficiente:** Fazer buscas por data, nível de erro ou usuário em arquivos de texto de centenas de megabytes exige comandos pesados de I/O (`grep`, `awk`, `sed`).
* **Concorrência:** Escritas concorrentes frequentes geram bloqueios ou corrupção de arquivos planos.

O **CI4 SQLite Logger** soluciona esses problemas gravando eventos diretamente em tabelas relacionais SQLite indexadas, implementando verificação matemática de custódia e execução em modo **WAL (Write-Ahead Logging)**.

---

## Recursos Principais

### 1. Corrente de Custódia (Hash Chain SHA-256)
Cada evento gravado calcula um hash criptográfico baseado no hash da linha imediatamente anterior combinado aos dados atuais:
```json
{Hash Atual} = {SHA256}{Hash Anterior} + {UUID} + {Nível} + {Mensagem} + {IP} + {Contexto} + {Protobuffers}
```

Se qualquer registro for excluído ou editado manualmente via terminal ou injeção, a cadeia se rompe, invalidando o banco de dados perante perícias digitais.
{: .warning }

### 2. Banco de Dados Rotativo Automático
Monitoramento contínuo do tamanho do arquivo `.db`. Ao atingir o teto parametrizado (padrão de **10 MB**), o arquivo atual é rotacionado de forma atômica e um novo banco é iniciado sem derrubar a aplicação.

### 3. Modo Performance (Write-Ahead Logging)
Configurado com `PRAGMA journal_mode = WAL;`, permitindo que leituras e escritas aconteçam simultaneamente em threads distintas sem causar gargalos (`database locked`).

### 4. Metadados e Trilha Forense
Captura automática de:
* Endereço IP do cliente (compatível com IPv4 e IPv6).
* Porta remota de conexão (`REMOTE_PORT`).
* *User-Agent* detalhado (navegador, dispositivo e sistema operacional).
* Identificador único universal (**UUIDv4**) por linha de log.
* Metadados de segurança para prevenir a substituição física do arquivo do banco.

> **Requisitos do Ambiente:** PHP 8.1+, extensão `ext-sqlite3` habilitada e [CodeIgniter 4.x](https://codeigniter.com/user_guide/ "CodeIgniter 4 User Guide") instalado.
{: .note }

---

## Apoiado e mantido por

<div class="ms-container">
  <img src="https://cdn-a1-br-sl.meusistema.com.br/imagens/logo_ms.png" alt="MeuSistema sistemas online personalizados" class="ms-logo">
  <div class="ms-content">
    <p>
      <strong>Meu Sistema - Sistemas online personalizados</strong><br>
      Acesse: <a href="https://meusistema.com.br" target="_blank" title="Sistemas online personalizados">https://meusistema.com.br</a><br>
      Fale conosco em: contato[at]meusistema.com.br
    </p>
  </div>
</div>

## Licença

[MIT](https://choosealicense.com/licenses/mit/)
