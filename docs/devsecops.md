# Pipeline DevSecOps

O workflow [`.github/workflows/devsecops.yml`](../.github/workflows/devsecops.yml)
executa em toda abertura ou atualização de Pull Request para `main` e em todo
push na própria `main`.

## Ferramentas e regras de bloqueio

### Gitleaks

Analisa todo o histórico Git em busca de senhas, tokens e chaves. Qualquer
possível segredo encontrado faz o job falhar.

### Testes e Ruff

Executa os testes automatizados, mede a cobertura e verifica o código com o
Ruff. Uma falha nos testes ou no lint bloqueia o pipeline.

### SonarCloud

Realiza a análise estática do código e aguarda o resultado do Quality Gate.
Código novo que não atende aos critérios definidos reprova o job.

### Trivy

Analisa as dependências do projeto e a imagem Docker. Vulnerabilidades `HIGH` e
`CRITICAL` com correção disponível bloqueiam o pipeline. Todas as severidades
ficam registradas nos artifacts `trivy-dependencias` e `trivy-imagem`.

### Syft

Gera o arquivo `sbom-imagem.spdx.json` com os componentes e versões presentes
na imagem e o publica como artifact por 30 dias.

A imagem `ghcr.io/paulohsilveir/ufla-devops-shop` só é enviada ao GHCR em um push na
`main`, depois que Gitleaks, testes, SonarCloud, as duas análises do Trivy e a
geração do SBOM terminam com sucesso. Pull Requests apenas constroem e analisam
a imagem.

## Quality Gate e proteção da branch

O projeto usa um Quality Gate para código novo com avaliação A em segurança,
confiabilidade e manutenibilidade, 100% dos hotspots revisados, cobertura de no
mínimo 80% e no máximo 3% de duplicação. O parâmetro
`sonar.qualitygate.wait=true` transforma uma reprovação em falha do job.

A proteção da `main` deve exigir Pull Request e estes checks:

- `Segredos / Gitleaks`;
- `Testes e SAST / SonarCloud`;
- `Dependencias / Trivy`;
- `Imagem / Trivy, Syft e publicacao`.

## Evidências

As capturas da execução bloqueada, do relatório da vulnerabilidade, da execução
aprovada e do artifact SBOM estão documentadas em
[`docs/evidencias/README.md`](evidencias/README.md).
