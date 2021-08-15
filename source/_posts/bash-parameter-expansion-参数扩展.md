title: bash parameter expansion 参数扩展
author: listenerri
categories:
  - bash
tags:
  - parameter expansion
  - 表达式扩展
  - bash
date: 2021-08-15 11:43:00
---
摘自 bash man 手册

字符 `$` 引入了参数扩展，命令替换和算术扩展。要扩展的参数名或符号可能包含在花括号中，花括号可选的，但是可以使得要扩展的变量不会与紧随其后的字符合并，成为新的名称。

使用花括号的时候，匹配的右括号是第一个  `}`，并且它没有被反斜杠引用或包含在一个引用的字符串中，也没有包含在一个嵌入的算术扩展，命令替换或是参数扩展中。

- `${parameter}`

    被替换为 parameter 的值。
    
    - 如果 parameter 是一个位置参数，并且数字多于一位时；或者当紧随 parameter 之后有不属于名称一部分的字符时，都必须加上花括号。
    - 如果 parameter 的第一个字符是一个感叹号，将引进一层间接变量。bash 使用以 parameter 的其余部分为名的变量的值作为变量的名称；接下来新的变量被扩展，它的值用在随后的替换当中，而不是使用 parameter 自身的值。这也称为 indirect expansion(间接扩展). 例外情况是下面讲到的 `${!prefix*}`。

下面的每种情况中，word 都要经过波浪线扩展，参数扩展，命令替换和算术扩展。如果不进行子字符串扩展，bash 测试一个没有定义或值为空的参数；忽略冒号的结果是只测试未定义的参数。

- `${parameter:-word}`

    Use Default Values(使用默认值)。如果 parameter 未定义或值为空，将替换为 word 的扩展。否则，将替换为 parameter 的值。
    
- `${parameter:=word}`

    Assign Default Values(赋默认值)。如果 parameter 未定义或值为空， word 的扩展将赋予 parameter. parameter 的值将被替换。位置参数和特殊参数不能用这种方式赋值。
    
- `${parameter:?word}`

    Display Error if Null or Unset(显示错误，如果未定义或值为空)。如果 parameter 未定义或值为空，word (或一条信息，如果 word 不存在) 的扩展将写入到标准错误；shell 如果不是交互的，则将退出。否则，parameter 的值将被替换。
    
- `${parameter:+word}`
    Use Alternate Value(使用可选值)。如果 parameter 未定义或值为空，不会进行替换；否则将替换为 word 扩展后的值。
    
- `${parameter:offset}` 和 `${parameter:offset:length}`

    Substring Expansion(子字符串扩展)。扩展为 parameter 的最多 length 个字符，从 offset 指定的字符开始。
    
    - 如果忽略了 length，扩展为 parameter 的子字符串，从 offset 指定的字符串开始。length 和 offset 是算术表达式 (参见下面的 ARITHMETICEVALUATION 算术求值 段落)。length 必须是一个大于等于 0 的数值。
    - 如果 offset 求值结果小于 0，值将当作从 parameter 的值的末尾算起的偏移量。
    - 如果 parameter 是 @，结果是 length 个位置参数，从 offset 开始。
    - 如果 parameter 是一个数组名，以 @ 或 * 索引，结果是数组的 length 个成员，从 `${parameter[offset]}` 开始。子字符串的下标是从 0 开始的，除非使用位置参数时，下标从 1 开始。

- `${!prefix*}`

    扩展为名称以 prefix 开始的变量名，以特殊变量 IFS 的第一个字符分隔。

- `${#parameter}`

    替换为 parameter 的值的长度 (字符数目)。
    
    - 如果 parameter 是 * 或者是 @, 替换的值是位置参数的个数。
    - 如果 parameter 是一个数组名，下标是 * 或者是 @, 替换的值是数组中元素的个数。

- `${parameter#word}` 和 `${parameter##word}`

    word  被扩展为一个模式，就像路径扩展中一样。
    
    - 如果这个模式匹配 parameter 的值的起始，那么扩展的结果是将 parameter 扩展后的值中，最短的匹配 (\`\`#'' 的情况) 或者最长的匹配 (\`\`##''的情况) 删除的结果。
    - 如果 parameter 是 @ 或者是 *, 则模式删除操作将依次施用于每个位置参数，最后扩展为结果的列表。
    - 如果 parameter 是一个数组变量，下标是 @ 或者是 *, 模式删除将依次施用于数组中的每个成员，最后扩展为结果的列表。

- `${parameter%word}` 和 `${parameter%%word}`

    word 被扩展为一个模式，就像路径扩展中一样。
    
    - 如果这个模式匹配 parameter 扩展后的值的尾部，那么扩展的结果是将 parameter 扩展后的值中，最短的匹配 (\`\`%'' 的情况) 或者最长的匹配 (\`\`%%''的情况) 删除的结果。
    - 如果 parameter 是 @ 或者是 *, 则模式删除操作将依次施用于每个位置参数，最后扩展为结果的列表。
    - 如果 parameter 是一个数组变量，下标是 @ 或者是 *, 模式删除将依次施用于数组中的每个成员，最后扩展为结果的列表。

- `${parameter/pattern/string}` 和 `${parameter//pattern/string}`

    pattern 被扩展为一个模式，就像路径扩展中一样。parameter 被扩展，其值中最长的匹配 pattern 的内容被替换为 string。在第一种形式中，只有第一个匹配被替换。第二种形式使得 pattern 中所有匹配都被替换为 string。
    
    - 如果 pattern 以 #  开始，它必须匹配 parameter 扩展后值的首部。
    - 如果 pattern 以 % 开始，它必须匹配 parameter 扩展后值的尾部。
    - 如果 string 是空值，pattern 的匹配都将被删除， pattern 之后的 / 将被忽略。
    - 如果 parameter 是 @ 或者是 *, 则替换操作将依次施用于每个位置参数，最后扩展为结果的列表。
    - 如果 parameter 是一个数组变量，下标是 @ 或者是 *, 模式删除将依次施用于数组中的每个成员，最后扩展为结果的列表。