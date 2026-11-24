# gitceshi

测试仓库。远程：<https://github.com/Michael-Durant/gitceshi>

## 目录布局（重要）

这个工作目录位于 OneDrive 同步范围内，而 OneDrive 没有「按目录排除同步」的功能
（官方只提供「选择文件夹」，取消勾选会把本地文件一并删掉），所以把**最怕同步的
`.git` 目录挪到了同步范围外**，目录里只留一个指针文件：

```
D:\one drive\OneDrive\Desktop\git\        <- 工作目录（同步范围，只有 3 个文件）
├── .git                                  <- 32 字节文本文件，内容是 gitdir 指针
├── .gitignore
├── README.md
└── .vscode\                              <- 已被 .gitignore 忽略，不会上传

D:\git-data\gitceshi.git\                 <- 真正的 git 元数据（同步范围外）
```

`.git` 文件内容：

```
gitdir: D:\git-data\gitceshi.git
```

注意：这个文件必须是 **UTF-8 无 BOM** 编码。用 PowerShell 写时不要用 `-Encoding ascii`，
否则路径里的中文（例如用户目录 `C:\Users\张礼晨\...`）会被写成 `???`，git 直接报
`fatal: not a git repository`。正确写法：

```powershell
$utf8NoBom = New-Object System.Text.UTF8Encoding($false)
[IO.File]::WriteAllText((Join-Path $repo '.git'), "gitdir: $gitDir", $utf8NoBom)
```

git 原生支持这种写法（`git worktree` / submodule 用的就是它），所有命令照常：

```powershell
git -C 'D:\one drive\OneDrive\Desktop\git' status
git -C 'D:\one drive\OneDrive\Desktop\git' push
```

## 在别的电脑上使用

**不要**直接依赖这个 `.git` 指针文件——它指向的是本机的 `D:\git-data`。
换机器时重新克隆：

```bash
git clone https://github.com/Michael-Durant/gitceshi.git
```

## 想恢复成「普通的」仓库

把元数据移回原处，再删掉指针文件：

```powershell
Move-Item 'D:\git-data\gitceshi.git' 'D:\one drive\OneDrive\Desktop\git\.git.new'
Remove-Item 'D:\one drive\OneDrive\Desktop\git\.git'
Rename-Item 'D:\one drive\OneDrive\Desktop\git\.git.new' '.git'
```

（更好的做法仍然是：把整个仓库放到非 OneDrive 路径，例如 `D:\repos\gitceshi`。）

## 使用

```bash
git clone https://github.com/Michael-Durant/gitceshi.git
```
