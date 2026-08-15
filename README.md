<div align="center">
  <img src="docs/logo.png" alt="legacy-showcase" width="180" height="180" style="border-radius: 1rem;">
  <h1 align="center">legacy-showcase</h1>
  <p align="center">
    ERP desktop em Windows Forms com SQLite embarcado, importação de TXT e CSV<br>
    e exportação para planilha, com a mesma base de código rodando no navegador.
  </p>
  <a href="https://dotnet.microsoft.com"><img src="https://img.shields.io/badge/.NET-10.0_LTS-512BD4?style=flat-square&logo=dotnet&logoColor=white" alt=".NET 10 LTS" /></a>
  <a href="https://learn.microsoft.com/dotnet/csharp/"><img src="https://img.shields.io/badge/C%23-14-239120?style=flat-square&logo=csharp&logoColor=white" alt="C# 14" /></a>
  <a href="https://learn.microsoft.com/dotnet/desktop/winforms/"><img src="https://img.shields.io/badge/windows_forms-net10.0--windows-0078D4?style=flat-square&logo=windows&logoColor=white" alt="Windows Forms" /></a>
  <a href="https://learn.microsoft.com/aspnet/core/blazor/"><img src="https://img.shields.io/badge/blazor-webassembly-512BD4?style=flat-square&logo=blazor&logoColor=white" alt="Blazor WebAssembly" /></a>
  <a href="https://learn.microsoft.com/ef/core/"><img src="https://img.shields.io/badge/sqlite-ef_core_10-003B57?style=flat-square&logo=sqlite&logoColor=white" alt="SQLite via EF Core 10" /></a>
  <br>
  <img src="https://img.shields.io/badge/versão-0.12.0-blue?style=flat-square" alt="Versão 0.12.0" />
  <a href="../../releases/latest/download/LegacyShowcase-windows-x64.exe"><img src="https://img.shields.io/badge/baixar-Windows-0078D4?style=flat-square&logo=windows&logoColor=white" alt="Baixar para Windows" /></a>
  <a href="https://ls.thiagocaja.dev"><img src="https://img.shields.io/badge/ERP-no_navegador-22C55E?style=flat-square&logo=amazons3&logoColor=white" alt="ERP no navegador" /></a>
</div>

<br>

Espelho público de distribuição do **legacy-showcase**. Aqui moram só os binários;
o código-fonte vive no repositório de desenvolvimento.

> **Versão publicada: v0.12.0**
>
> Este repositório guarda **uma versão só**. Cada publicação apaga a release e a
> tag anteriores, então o que está em [Releases](../../releases/latest) é sempre o
> estado corrente do `main`. O nome do arquivo não carrega versão — o `vX.Y.Z` mora
> só na tag —, o que mantém `releases/latest/download/` como endereço permanente. O
> histórico de mudanças fica no CHANGELOG do repositório de desenvolvimento, não
> aqui.

## Baixar

| Plataforma | Arquivo | Requisito |
| :-- | :-- | :-- |
| Windows | [`LegacyShowcase-windows-x64.exe`](../../releases/latest/download/LegacyShowcase-windows-x64.exe) | Windows 10 ou 11, 64 bits |
| Linux e macOS | (sem download) | atendido pela [versão web](https://ls.thiagocaja.dev) |

**Por que não há Linux nem macOS.** Windows Forms é uma camada sobre a API de
janelas do Windows, e não existe fora dela — é justamente essa superfície Win32
que o projeto se propõe a demonstrar. Trocar o framework por um multiplataforma
apagaria o assunto. Quem não está no Windows abre a versão no navegador, que roda
o mesmo núcleo de domínio compilado para WebAssembly.

## O que esperar ao abrir

**Não é instalador, é o programa.** O download é o executável, e abrir é executar
— nada é instalado, nada vai para o registro, e apagar o arquivo desfaz tudo.

**O SmartScreen avisa na primeira execução**, porque o executável não é assinado
por certificado de código. Clique em **Mais informações** e depois em **Executar
assim mesmo**.

**Nada precisa ser instalado antes.** O pacote é self-contained e de arquivo
único: o runtime .NET vai embutido. A primeira execução é mais lenta que as
seguintes, porque o conteúdo é extraído para uma pasta temporária antes de rodar.

**O banco nasce vazio e se povoa sozinho.** Um SQLite é criado em
`%LOCALAPPDATA%\legacy-showcase\legacy.db` na primeira execução, as migrações
rodam no start e uma carga de demonstração entra com clientes e lançamentos. Ele
não fica ao lado do executável de propósito: o programa pode ser aberto de uma
pasta somente leitura ou de rede, e um banco embarcado precisa de lugar gravável.
A credencial de acesso já vem preenchida na tela de login.

## Atualizar de uma versão anterior

Baixe o arquivo novo e substitua o antigo. **Não há desinstalação**, porque não
houve instalação.

Como o banco fica na pasta de dados do usuário e não junto do executável, **os
dados sobrevivem à troca** — as migrações pendentes rodam no primeiro start da
versão nova. Feche o programa antes de substituir o arquivo, ou o Windows recusa a
escrita. Para começar do zero, apague a pasta `%LOCALAPPDATA%\legacy-showcase`.

## O aplicativo

Um showcase de ERP legado: login, usuários, clientes e lançamentos financeiros,
sobre um SQLite embarcado. O centro dele é o que o trabalho real pede — importar
arquivo TXT de posição fixa e CSV, com recusa relatada linha a linha, e exportar
para planilha.

- **Domínio:** `User`, `Customer` (nome, documento, contato, endereço) e
  `FinancialEntry` (tipo, valor, cliente, vencimento, baixa)
- **Interface:** Windows Forms com janela MDI, tema claro e escuro, escala de
  texto ajustável e busca por `Ctrl+K`
- **Dados:** SQLite local via EF Core, migrações no start e carga de demonstração
- **Arquivos:** importação de TXT de posição fixa e de CSV, com Windows-1252 e
  Latin-1 lidos corretamente; exportação para XLSX
- **Acesso:** credencial de demonstração, já preenchida na tela de login

Os dados nascem e morrem na máquina. Não há servidor, não há sincronização, nada
é enviado para lugar nenhum.

## Padrão do projeto

O repositório de desenvolvimento é um núcleo compartilhado com duas frentes de
entrega:

```
src/Domain          entidades e objetos de valor, sem dependência de framework
src/Application     casos de uso e as interfaces de repositório
src/Infrastructure  EF Core e SQLite, para a frente desktop
src/FileInterop     leitores de TXT e CSV, escritores de planilha
src/Desktop.WinForms  frente desktop (Windows Forms), este download
src/Web.Blazor        frente web (Blazor WebAssembly), publicada como site estático
tests/              xUnit v3 com Shouldly, sobre o núcleo
```

A regra que sustenta o arranjo: **as duas frentes não possuem nada além de
apresentação**. Repositórios são interfaces declaradas em `Application`, com uma
implementação em EF Core para o desktop e outra em memória para o navegador, e
trocar uma pela outra não toca em nenhum caso de uso. É isso que permite o mesmo
domínio rodar como janela Win32 e como página.

## Stack

.NET 10 · C# 14 · Windows Forms · Blazor WebAssembly · EF Core 10 · SQLite ·
ClosedXML · Argon2id

## Licença

Os binários seguem a licença do repositório de desenvolvimento.
