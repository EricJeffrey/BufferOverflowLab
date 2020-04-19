缓冲区溢出攻击实验。

# 前置条件
1. 确保kernel.randomize_va_space=0 -> `sysctl -w kernel.randomize_va_space=0`
2. stack.c编译选项
   ```
   $ su root
   Password (enter root password)
   # gcc -o stack -z execstack -fno-stack-protector stack.c
   # chmod 4755 stack
   # exit
   ```
3. exploit.c正常编译

# 具体流程

原理: `strcpy`由于不检查边界，因此会覆盖函数的返回地址。函数返回是，会读取覆盖的返回地址中的指令执行，即执行shellcode。

1. 使用gdb调试确定缓冲区地址addr
2. 计算 **buffer地址** 与 **函数返回地址** 的距离offset
3. 攻击字符串`str+offset = addr+offset+4+1`，其余为`0x90`无操作

# 攻击流程
1. 编译exploit.c与stack.c，参照前置条件
2. 执行exploit，生成badfile
3. 执行stack，实现攻击
