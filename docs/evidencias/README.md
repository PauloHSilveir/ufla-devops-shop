# Evidências do pipeline DevSecOps

## Execução inicial aprovada

A primeira execução do workflow no Pull Request 1 concluiu com sucesso os
checks do Gitleaks, SonarCloud, Trivy e Syft. A página também apresenta os
artifacts `trivy-dependencias`, `trivy-imagem` e `sbom-imagem.spdx.json`.

[Abrir execução no GitHub Actions](https://github.com/PauloHSilveir/ufla-devops-shop/actions/runs/40622212700)

![Execução inicial aprovada e artifacts publicados](execucao-inicial-aprovada.png)

As evidências do bloqueio por vulnerabilidade e da aprovação após a correção
serão acrescentadas após o Pull Request de demonstração.
