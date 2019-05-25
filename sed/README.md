## sed高级命令分为三块

* 处理多模式空间（N,D，P）

* 采用保持空间来保持模式空间的内容并使它可用于后续的命令(H,h,G,g,x)

* 编写使用分支和条件指令的脚本来更改控制了(:,b,t)

通常来说，一行被读进模式空间，并且用脚本中的每个命令(一个接一个地)应用于该行。当到达脚本底部时，输出这一行，并清空模式空间。然后新行被读入模式空间，并且控制被转移回脚本的顶端。

也就是说，一般情况下，行的读入，命令的执行都是按照流程一步步来的。当然基础部分也有两个例外，可以改变流程。

一个是d命令，d命令将会清空模式空间，并导致读入新的输入行，此时控制将忽略d之后的命令，并转移到脚本的顶端作用在新输入行上。

第二个是n命令，可以说n命令和d命令有一点点类似，不同的是n命令执行的时候，会读入下一行替换当前行，替换之前会把当前行输出。而替换之后，n命令之后的命令会作用到新行上


| 命令 | 用法 |
| - | - |
| N | 读取新的输入行，并将该行追加到模式空间现有内容之后，来创建多行模式空间。创建后的多行模式空间中，原有内容和新内容用换行符"\n"来分割。而执行N命令之后，将会继续执行，N之后的命令，N之后命令的对象则是我们新的模式空间的内容。注意：多行模式空间中，^匹配整个空间的开始，$匹配整个空间的结尾，比如上面的 1\n2\n3 开头是1，结尾是3$!N,最后一行，不执行N命令 |
| D | 删除模式空间中，从头开始到第一个嵌入的换行符为止。它并不会导致新行的输入，而是会返回脚本的顶端，将这些指令应用于空间中剩余的内容 |
| P | 输出模式空间中，从头开始到第一个嵌入的换行符为止。实际运用中，P经常放到N之后，P之前 |
| n | 输出当前模式空间的内容，读取下一行替换当前行，替换之后，n命令之后的命令作用到新行上 |
| d | 情况模式空间，并导致新行的读入，此时控制将忽略d之后的命令，并转移到脚本的顶端，作用在新的输入行上 |
| p | 打印整个模式空间的内容。其他的对模式空间，没啥影响了。 |
### 将sample1中的@f1(anything)形式的内容，替换为\fB(anything)\fR的形式（包括跨行）


	➜  sed git:(master) ✗ sed '
	quote> s/f1(\([^)]*\))/\\fB(\1)\\fR/g
	quote> /f1(.*/{
	quote> N
	quote> s/f1(\(.*\n[^)]*\))/\\fB(\1)\\fR/g
	quote> P
	quote> D
	quote> }' sample1 
说明：在sed中，换行相当于“; -e"
输出为：
	
	


	I want to see @f1(what will happen) if we put the                             
	font change commands @f1(on a set of lines). if I understand                  
	things (correctly), the @f1(third) line causes problems. (No?).               
	ls this really the case, or is it (maybe) just something else?                
	
	Let's test having two on a line @f1(here) and @f1(three) as                    
	well as one that begins on one line and ends @f1(somewhere
	on another line). What if @f1(it is here) on the line?
	Another @f1(one).

