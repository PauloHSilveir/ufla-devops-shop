# Evidências do pipeline DevSecOps

## Execução inicial aprovada

A primeira execução do workflow no Pull Request 1 concluiu com sucesso os
checks do Gitleaks, SonarCloud, Trivy e Syft. A página também apresenta os
artifacts `trivy-dependencias`, `trivy-imagem` e `sbom-imagem.spdx.json`.

[Abrir execução no GitHub Actions](https://github.com/PauloHSilveir/ufla-devops-shop/actions/runs/40622212700)

![Execução inicial aprovada e artifacts publicados](execucao-inicial-aprovada.png)

## Bloqueio da dependência vulnerável

No Pull Request 3 foi adicionada a dependência `requests==2.19.1`. A execução
falhou no check `Dependencias / Trivy`, e o job da imagem foi impedido de
iniciar.

[Abrir Pull Request bloqueado](https://github.com/PauloHSilveir/ufla-devops-shop/pull/3)

[Abrir execução bloqueada](https://github.com/PauloHSilveir/ufla-devops-shop/actions/runs/40623594841)

![Execução bloqueada pelo Trivy](execucao-bloqueada.png)

O relatório do Trivy identificou a vulnerabilidade `CVE-2018-18074`, de
severidade `HIGH`, na versão `2.19.1` do pacote `requests`. O próprio relatório
indica a versão `2.20.0` como primeira versão corrigida. A captura abaixo foi
gerada localmente com o mesmo comando e a mesma base de vulnerabilidades usados
pelo workflow; o relatório JSON da execução também está disponível no artifact
`trivy-dependencias`.

![Relatório da vulnerabilidade identificada](relatorio-cve.png)

## Correção

A evidência da execução aprovada será acrescentada após a atualização da
dependência no mesmo Pull Request.
