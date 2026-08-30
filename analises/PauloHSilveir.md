# Autópsia: Cloudflare (02/07/2019)

**Autor:** Paulo Henrique dos Anjos Silveira (@PauloHSilveir)
**Fonte primária:** https://blog.cloudflare.com/details-of-the-cloudflare-outage-on-july-2-2019/
**Data de acesso:** 30/08/2026

## 1. O que aconteceu

Às 13:42 UTC, uma nova regra de segurança foi enviada para toda a rede da Cloudflare.
A regra consumiu quase toda a capacidade dos processadores e vários sites começaram a retornar erro 502.
Às 13:45, apenas três minutos depois, os primeiros alertas indicaram que havia um problema.
Às 14:07, a equipe desligou o componente afetado e, às 14:09, o tráfego já havia voltado ao normal.
Depois de identificar e testar a correção, o sistema de proteção foi totalmente reativado às 14:52.

## 2. Qual das Três Vias falhou

A principal falha foi na **Via do Feedback**. Antes da implantação, o pipeline executou os testes normalmente e deu sinal verde, mas esses testes verificavam se as regras bloqueavam ou liberavam requisições corretamente, sem verificar consumo excessivo de CPU. Ou seja, havia feedback, mas ele não mostrava o risco mais importante daquela mudança. O problema só apareceu depois que a regra já estava distribuída globalmente.

## 3. Quais métricas DORA teriam denunciado antes

As métricas mais relevantes seriam Change Failure Rate e Time to Restore Service. A primeira ajudaria a acompanhar quantas mudanças do WAF causavam falhas ou precisavam ser revertidas. A segunda revelaria a dificuldade de recuperação, já que o próprio relatório mostra que o rollback era lento e dependia de reconstruir o WAF.

Mas existe uma limitação: o relatório não mostra que essas métricas já estavam historicamente ruins. Na verdade, a Cloudflare fazia centenas de alterações no WAF e não tinha uma queda global havia seis anos. Portanto, DORA poderia mostrar problemas de estabilidade, mas não substituiria testes e barreiras específicas para esse tipo de mudança.

## 4. Qual prática do semestre teria evitado — e em que semana

**Semana 8 — Entrega e Implantação Contínuas**

Essa prática teria reduzido diretamente o alcance do incidente. A própria Cloudflare já usava um processo progressivo para outros softwares: primeiro a mudança passava por uma região usada por funcionários (DOG), depois por uma pequena parcela de clientes (PIG), depois por três ambientes Canary e só então era distribuída globalmente. O WAF era uma exceção e podia ir diretamente para o mundo inteiro porque precisava responder rapidamente a novas ameaças.

Se a nova regra tivesse sido liberada primeiro para uma pequena parcela dos servidores, o aumento de CPU poderia ter sido percebido antes de atingir toda a rede. Isso fica ainda mais claro porque uma das ações tomadas depois do incidente foi justamente passar a usar *staged rollout* também nas regras do WAF.

## 5. A cultura do relatório: generativa ou patológica?

Considero a cultura apresentada no relatório generativa. A Cloudflare não trata o engenheiro que escreveu a regra como a única causa do incidente. Pelo contrário, questiona o próprio processo e lista problemas nos testes, na implantação, no rollback, nos alertas e nos procedimentos de emergência.

Um trecho que deixa essa postura clara é:

> “Getting to a single root cause, while satisfying, may obscure the reality.”

A frase mostra que a empresa evita explicar o incidente apenas pelo erro de uma pessoa. O erro na expressão regular foi o gatilho, mas o relatório procura entender por que o sistema permitiu que esse erro chegasse à rede inteira. Essa postura está mais próxima de uma cultura generativa, que usa a falha para aprender e melhorar o sistema, do que de uma cultura patológica baseada em procurar culpados.
