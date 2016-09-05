# Windows 使用命令修复 U 盘/存储卡

树莓派的存储卡意外损坏，原本32G 的空间只能识别成1G。

## 使用 diskpart 修复存储卡

- 在命令提示符下输入 *diskpart* 进入；
- 列出所有磁盘：*list disk*；
- 注意磁盘的原大小，找出存储卡的磁盘进行选择：*select disk 3*；
- 再次列出所有磁盘，被选中的磁盘前有号：*list disk*；
- 清除此磁盘：*clean*；
- 创建分区：*creat partition primary* ；
- 格式化磁盘：*format quick fs=fat32* ；

