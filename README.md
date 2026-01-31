# Projeto IA para DEV: Tela de enviar comentários
Projeto criado no curso "IA para Dev: Desenvolvendo Códigos com ChatGPT, Grok, Claude e Gemini"! Durante as aulas, aprendi a explorar ferramentas incríveis como o ChatGPT e o Google Gemini para otimizar o desenvolvimento de códigos. Além disso, apliquei técnicas de refatoração e validação em tempo real, melhorei a geração de protótipos funcionais e a correção dinâmica dos códigos. Compreendi a importância de criar prompts precisos para obter respostas eficazes e experimentei a integração com ambientes como Google Colab. Utilizando linguagens como Python e JavaScript, consegui fazer ajustes e melhorias significativas no meu código. 
O projeto pode ser visualizado atraves do link abaixo:
[https://claude.ai/public/artifacts/2888c72e-fce7-4ab6-aa16-b3b36b16790a](TelaEnviarComentarios)

---
# Documentação Técnica - Sistema de Avaliação com Censura de Palavras

## 🎯 Visão Geral

Sistema web de avaliação de compras que implementa um formulário interativo com slider de sentimento, censura automática de palavras proibidas.

**Tecnologias:** HTML5, JavaScript ES6+, Tailwind CSS

**Características principais:**
- Interface de avaliação em 3 níveis (Muito Bom, Regular, Ruim)
- Sistema de censura de palavras em tempo de submissão
- Mudança dinâmica de cores e emojis baseada no sentimento
- Validação de formulário com feedback visual
- Transição suave entre estados da interface

---

## ⚙️ Funcionalidades Principais

#### 1. Sistema de Slider de Sentimento

**Especificação técnica:**
- Range Input HTML5 com 3 valores discretos (0, 1, 2)
- Mapeamento: 0 = Muito Bom, 1 = Regular, 2 = Ruim
- Event listener em modo `input` para atualização em tempo real


#### 2. Interface Responsiva a Sentimento

**Mapeamento de Estados:**

| Valor | Emoji | Cor de Fundo | Classificação | Cor de Borda |
|-------|-------|--------------|---------------|--------------|
| 0     | 😄    | `bg-green-500` | Muito Bom     | `border-green-500` |
| 1     | 😐    | `bg-yellow-400` | Regular       | `border-yellow-400` |
| 2     | 😡    | `bg-red-500` | Ruim          | `border-red-500` |

**Objeto de Configuração:**

```javascript
const states = {
    0: {
        colorClass: 'bg-green-500',
        borderColor: 'border-green-500',
        emoji: '😄'
    },
    1: {
        colorClass: 'bg-yellow-400',
        borderColor: 'border-yellow-400',
        emoji: '😐'
    },
    2: {
        colorClass: 'bg-red-500',
        borderColor: 'border-red-500',
        emoji: '😡'
    }
};
```

#### 3. Validação de Formulário

**Regras de validação:**
1. Campo de comentário é **obrigatório**
2. Comentários vazios ou apenas espaços em branco são rejeitados
3. Uso de `.trim()` para eliminar espaços extras

**Feedback visual de erro:**
- Mensagem de erro com ícone SVG
- Borda vermelha no campo de entrada
- Ring (outline) vermelho no campo
- Auto-focus no campo com erro
- Auto-remoção do erro ao começar a digitar

---

## 🔧 Componentes Complexos

## Sistema de Censura de Palavras

Implementação de filtro de conteúdo que substitui palavras proibidas por asteriscos, preservando o comprimento original da palavra para manter contexto visual.

### Estrutura de Dados

```javascript
const palavrasProibidas = [
    'teste',
    'segredo',
    'senha',
    'confidencial',
    'uva',
    'proibido',
    'idiota',
    'burro',
    'estúpido'
];
```

**Características:**
- Array estático de strings
- Case-insensitive (detecta maiúsculas/minúsculas)
- Facilmente extensível

### Algoritmo de Censura

**Função principal:**

```javascript
function censurarTexto(texto, palavras) {
    let textoCensurado = texto;

    palavras.forEach(palavra => {
        const asteriscos = '*'.repeat(palavra.length);
        const regex = new RegExp(`\\b${palavra}\\b`, 'gi');
        textoCensurado = textoCensurado.replace(regex, asteriscos);
    });

    return textoCensurado;
}
```

**Análise técnica:**

1. **`'*'.repeat(palavra.length)`**
   - Gera string de asteriscos com comprimento idêntico à palavra
   - Mantém legibilidade do contexto sem revelar palavra
   - Exemplo: "senha" → "*****"

2. **RegExp com word boundaries (`\\b`)**
   - `\\b` = word boundary (início ou fim de palavra)
   - Previne substituições parciais
   - Exemplo: "confidencial" em "semiconfidencial" não é substituído
   
3. **Flags da RegExp:**
   - `g` = global (todas as ocorrências)
   - `i` = case-insensitive (ignora maiúsculas/minúsculas)

**Casos de uso:**

```javascript
// Entrada
"A senha é Confidencial e o TESTE foi um segredo"

// Saída
"A ***** é ************ e o ***** foi um *******"
```


### Função de Submissão (submitFeedback)

**Fluxo completo:**

```javascript
function submitFeedback() {
    // 1. Captura e validação
    const comment = commentInput.value.trim();
    
    if (!comment) {
        errorMessage.classList.remove('hidden');
        commentInput.classList.add('border-red-500', 'ring-2', 'ring-red-500');
        commentInput.focus();
        return;  // Early return pattern
    }

    // 2. Captura de contexto
    const currentValue = rangeInput.value;
    const state = states[currentValue];

    // 3. CENSURA DO COMENTÁRIO
    const comentarioCensurado = censurarTexto(comment, palavrasProibidas);

    // 4. Preparação da tela de resultado
    submittedComment.textContent = `"${comentarioCensurado}"`;

    // 5. Aplicação da cor da avaliação no resultado
    resultCard.className = `bg-gray-100 rounded-lg p-4 text-left border-l-4 ${state.borderColor}`;

    // 6. Transição de estado
    formSection.classList.add('hidden');
    thankYouSection.classList.remove('hidden');
    thankYouSection.classList.add('animate-fade-in-up');
}
```

**Análise de complexidade:**

1. **Early return pattern:**
   - Validação logo no início
   - Retorno imediato se inválido
   - Reduz nesting e melhora legibilidade

2. **Estado imutável:**
   - Não modifica comentário original
   - Cria nova variável censurada
   - Permite rastreamento de ambas versões se necessário

3. **Remoção + adição de classes:**
   - `resultCard.classList.remove()` limpa cores anteriores
   - Reatribuição completa via `className` garante estado limpo
   - Importante: `className` substitui todas as classes

4. **Transição de estado unidirecional:**
   - Formulário → Agradecimento (sem volta automática)
   - Usuário deve recarregar página para nova avaliação
   - Pattern adequado para submissões de formulário


---

## Extensão do Sistema

**Como adicionar palavras:**

```javascript
// Método 1: Diretamente no array
const palavrasProibidas = [
    'palavra1',
    'palavra2',
    'novaPalavra'  // Adicionar aqui
];

// Método 2: Carregamento dinâmico (futuro)
fetch('palavras_proibidas.json')
    .then(res => res.json())
    .then(palavras => {
        // Usar palavras carregadas
    });
```

**Variações avançadas:**

```javascript
// Censura parcial (apenas primeiras letras)
const asteriscos = palavra[0] + '*'.repeat(palavra.length - 1);
// "senha" → "s****"

// Censura com hint
const asteriscos = palavra[0] + '*'.repeat(palavra.length - 2) + palavra[palavra.length - 1];
// "senha" → "s***a"

// Censura com caractere personalizado
const asteriscos = '█'.repeat(palavra.length);
// "senha" → "█████"
```



## Conclusão

Este sistema demonstra a implementação de conceitos avançados de desenvolvimento web frontend:

1. **Gestão de estado:** Transição entre múltiplos estados da UI
2. **Programação funcional:** Uso de `forEach`, arrow functions, template literals
3. **RegExp avançado:** Pattern matching com word boundaries
4. **CSS moderno:** Transições, transformações, glass morphism
5. **UX thoughtful:** Feedback visual, validação progressiva, micro-interações

O código está estruturado para fácil manutenção e extensão, seguindo princípios SOLID e patterns estabelecidos da indústria.

**Próximos passos recomendados:**
- Implementar testes unitários (Jest)
- Adicionar integração com backend (REST API)
- Implementar sistema de logging
- Criar versão com framework (React/Vue)
- Implementar acessibilidade completa (WCAG 2.1)
