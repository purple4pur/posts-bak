# VSCode 配置 verilog 环境

## 写在前面

听起来好像有些多此一举，因为其他专门负责硬件的程序已经能提供一站式的服务了，比如 ISE。确实，我自己也觉得并不是特别的有必要，当初有这个想法的时候也只是觉得好玩，但是现在它能做到的效果我觉得还是挺不错的，完全可以把写 .v 和测试输出的部分放到 VSCode 里来做。在顺手的编辑器里写代码的确是享受。

配环境的过程其实在两周前就已经完成了，但直到现在才来写是因为那时候我还没接触过 ISE，对 verilog 语言也很陌生，这就导致一我不会写代码测试这套流程，二我也不知道即使可以运行，对于电工电子实验课会有帮助吗。这周的电工电子实验课差不多解决了上面的问题了，我认为还是有帮助的，所以趁周末到了分享一下。

Happy coding!

## 完成效果

1. 关键字高亮，识别语法错误
2. 出数字波形
3. 快捷键或鼠标运行代码，不用敲终端

当然也有遗憾：

1. 还没找到能用且好用的格式化工具
2. 上了规模的项目可能就不好用了，还得回到专业软件

![VSCode verilog 完成效果.png](https://i.loli.net/2020/03/27/Asni8BJyVfb9PQH.png)

## 配置过程

### 安装插件和软件

VSCode 插件：

1. [Verilog HDL/SystemVerilog](https://marketplace.visualstudio.com/items?itemName=mshr-h.VerilogHDL)
2. [Code Runner](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner)

Verilog HDL/SystemVerilog 提供核心的语法高亮和语法检查，Code Runner 完成终端的自动化。插一句，Code Runner 真的舒服，我有好多语言环境都用它设置了编译运行。

软件：

1. [Icarus Verilog](http://iverilog.icarus.com/) ( Windows 版本官方 [下载地址](https://bleyer.org/icarus/) )
2. [GTKWave](http://gtkwave.sourceforge.net/) ( Icarus Verilog 会附带安装 )

以上两个软件都是开源的，完全免费。Icarus Verilog 实现编译等功能，GTKWave 显示数字波形。再次感谢开发者和开源社区。

安装完成后把 iverilog.exe 和 gtkwave.exe 所在路径添加到环境变量，如果已经开着 VSCode 或者终端，记得重新打开 VSCode 或终端才会生效。测试能否运行：

```
> iverilog -h
> gtkwave --version
```

![测试 iverilog 和 gtkwave.png](https://i.loli.net/2020/03/27/CrVtmx6AQLqlfPj.png)

### 配置插件

#### Verilog HDL/SystemVerilog

```json
"verilog.linting.linter": "iverilog"
```

![verilog linter 设置.png](https://i.loli.net/2020/03/27/mNLUSijF8hk26aW.png)

#### Code Runner

```json
"code-runner.saveFileBeforeRun": true,
"code-runner.runInTerminal": true,
"code-runner.executorMapByGlob": {
  "pom.xml": "cd $dir && mvn clean package",
  "*.test.js": "tap",
  "*.js": "node",
  // 前三条是默认值
  "*.v": "cd $dir && iverilog -o run.vvp \"$fileName\" && vvp run.vvp",
  "*.vcd": "cd $dir && gtkwave \"$fileName\"",
}
```

第一项看自己习惯，第二项建议设为 `true`，因为在输出面板是没办法输入的。最后一项只能到 `settings.json` 里手动添加。

### 测试功能

来一个前面完成效果的 `counter.v`，代码太长了，放在后面附录。在 .v 文件里执行 Run Code，可以点右上角的小三角，可以右键菜单里运行，也可以快捷键 `Ctrl + Alt + N`。

![iverilog 输出.png](https://i.loli.net/2020/03/27/eU9h3tPuKElirLx.png)

执行完成后在当前目录下会生成两个文件 `run.vvp` 和 `run.vcd`。右键 `run.vcd` 执行 Run Code。

![右键菜单 Run Code 选项.png](https://i.loli.net/2020/03/27/YG4BqZKvW2ROSJN.png)

在弹出的 GTKWave 窗口中，展开 `counter_tb`，右键 `tb`，并根据需要选择子菜单的 `Append` 或 `Insert` 或 `Replace`。

![GTKWave 添加目标变量.png](https://i.loli.net/2020/03/27/Qng3XBGtFzSYqRj.png)

此时右边的波形面板就会出现所有的变量了，鼠标滚轮移动前后，`Ctrl` 加滚轮放大缩小显示范围。选中变量右键，还可以更改数据格式。

![GTKWave 更改数据格式.png](https://i.loli.net/2020/03/27/DR8IgwFxXQtuG2V.png)

### 注意事项

1. 因为写死了命令，所以 .v 里 `$dumpfile` 的文件名必须是 `run.vcd`。
2. 为了避免执行无限地打印数据，一定要使程序在某个时刻结束，建议的方式是通过 `$finish` 直接指定结束时间：

   ```
   initial #1000 $finish;
   ```

3. 如果不慎发生了上一种情况，先在终端 `Ctrl + C` 打断执行，然后输入 `finish` 回车才能完全结束程序。

![打断 iverilog.png](https://i.loli.net/2020/03/27/vF4gyEwKIPplLh9.png)

## 附录

```
// counter.v

`timescale 1ns / 1ps

module counter(
  input clk,
  input clr,
  output [3:0] q
);
reg [3:0] q_temp;
assign q = q_temp;
always @(posedge clk or negedge clr)
  if (~clr)
    q_temp <= 4'b0000;
  else if (q_temp == 4'b1001)
    q_temp <= 4'b0000;
  else
    q_temp <= q_temp + 1'b1;
endmodule


module counter_tb;
reg clr;
reg clk;
wire [3:0] q;

counter tb(
  .clr(clr),
  .clk(clk),
  .q(q)
);

initial begin
  $dumpfile("run.vcd");
  $dumpvars;
  clr = 0;
  clk = 0;
  #40
  clr = 1;
end

always #10 clk = ~clk;

initial #500 $finish;
endmodule
```
