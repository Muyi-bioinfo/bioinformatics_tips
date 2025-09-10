---

# PyCharm 中使用 Git 进行版本控制

---

### 1. 前期准备：安装和配置 Git

在使用 PyCharm 的 Git 功能之前，请确保您的系统上已经安装了 Git。

*   **Windows:** 访问 [https://git-scm.com/](https://git-scm.com/) 下载并安装。安装时基本一直点击 “Next” 即可，但注意选择 “Git from the command line and also from 3rd-party software” 这个选项。
*   **macOS:** 可以通过 Homebrew (`brew install git`) 安装，或者从官网下载安装包。
*   **Linux (Ubuntu/Debian):** 使用命令 `sudo apt-get install git`。

安装完成后，打开终端（Terminal）或命令行（CMD），设置您的用户名和邮箱，这是提交代码时的身份标识：
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

---

### 2. 在 PyCharm 中配置 Git

1.  **打开设置：** 打开 PyCharm，进入 `File -> Settings` (Windows/Linux) 或 `PyCharm -> Preferences` (macOS)。
2.  **找到 Git 路径：** 导航到 `Version Control -> Git`。
3.  **指定 Git 可执行文件路径：** 在 “Path to Git executable” 一栏中，PyCharm 通常会自动检测到 Git 的安装路径。如果它没有自动找到，请手动浏览找到 `git.exe` (Windows) 或 `git` (macOS/Linux) 的位置（例如，Windows 上通常在 `C:\Program Files\Git\bin\git.exe`）。
4.  **测试：** 点击 “Test” 按钮，如果显示 Git 版本号，说明配置成功。
5.  **配置 GitHub（可选）：** 在 `Version Control -> GitHub` 中，您可以添加您的 GitHub 账户，方便进行远程仓库操作。




---

### 3. 核心操作：日常工作流

#### 场景一：初始化项目并提交到本地仓库

**方法 A: 将已有项目变为 Git 仓库**
1.  打开您的项目。
2.  点击顶部菜单栏的 `VCS -> Enable Version Control Integration...`。
3.  在下拉菜单中选择 `Git`，点击 OK。
4.  此时项目目录会变成棕色，表示它已是一个 Git 仓库，但文件还未被跟踪。

**方法 B: 从远程仓库（如 GitHub）克隆**
1.  在 PyCharm 欢迎界面选择 `Get from VCS`。
2.  或者，在界面内选择 `File -> New -> Project from Version Control...`。
3.  输入远程仓库的 URL（如 `https://github.com/username/repo.git`），选择本地存放目录，点击 “Clone”。

#### 提交（Commit）更改

1.  **做出修改：** 修改或创建一个新文件后，文件在 **Project** 窗口中的文件名会变成蓝色，表示已修改但未暂存。
2.  **暂存更改（Add to VCS）：** 右键点击文件或项目根目录，选择 `Git -> Add`。或者，在 **Commit** 窗口中勾选文件名前的复选框也会自动暂存。文件变成绿色，表示已暂存。
3.  **打开提交窗口：** 点击顶部工具栏的 **Commit** 按钮 (✔️)，或者使用快捷键 `Ctrl+K` (Windows/Linux) / `Cmd+K` (macOS)。
4.  **填写提交信息：** 在打开的Commit窗口中：
    *   查看已更改的文件列表。
    *   在下方输入有意义的提交信息（Commit Message），描述这次修改的内容。
    *   点击 **Commit** 按钮。此时更改就被提交到了**本地仓库**。



#### 推送（Push）到远程仓库

提交只是保存在本地，要与他人协作，需要推送到远程服务器（如 GitHub）。

1.  点击顶部工具栏的 **Push** 按钮 (⬆️)，或者使用快捷键 `Ctrl+Shift+K` (Windows/Linux) / `Cmd+Shift+K` (macOS)。
2.  在弹出的窗口中，确认要推送的分支和更改，点击 **Push**。
3.  如果是第一次推送，PyCharm 可能会提示您设置远程仓库的链接（如果项目是克隆的，则自动配置好）。

#### 拉取/更新（Pull/Fetch）远程更改

为了获取队友的更新，您需要定期拉取远程仓库的更改。

1.  点击顶部工具栏的 **Pull** 按钮 (↓)，或者使用快捷键 `Ctrl+T` (Windows/Linux) / `Cmd+T` (macOS)。
2.  Pull 操作相当于 `git fetch` + `git merge`，它会直接获取并尝试合并到您当前的分支。如果遇到冲突，PyCharm 会提示您解决。

---

### 4. 进阶操作

#### 分支（Branch）管理

*   **查看和切换分支：** 点击 PyCharm 窗口右下角的 **Git: master** 按钮，会弹出所有分支列表。你可以在这里轻松地切换（Checkout）到其他分支，或者创建新分支。



*   **合并分支（Merge）：** 首先切换到您想合并到的目标分支（如 `master`），然后从分支列表中选择要合并过来的分支（如 `feature-branch`），选择 `Merge into Current`。

#### 解决冲突（Conflict Resolution）

当您和队友修改了同一文件的同一区域时，拉取或合并会产生冲突。

1.  PyCharm 会检测到冲突并弹出提示。
2.  你可以点击 **Merge** 按钮，打开一个非常强大的三窗格对比工具。
3.  左侧是您的更改，右侧是对方的更改，中间是合并结果。
4.  使用箭头按钮选择您想要保留的更改，或者直接编辑中间的结果。
5.  解决完所有冲突后，点击 **Apply**。
6.  最后，像往常一样**提交（Commit）** 这次合并操作。

---

### 5. 常见问题与技巧

*   **.gitignore：** 对于 Python 项目，一定要配置 `.gitignore` 文件来忽略不需要版本控制的文件（如 `__pycache__/`, `.venv/`, `*.pyc` 等）。PyCharm 在创建项目时通常会提示你生成。
*   **查看历史：** 右键点击文件，选择 `Git -> Show History`，可以清晰看到该文件的所有提交记录和具体更改。
*   **撤销更改：** 在提交之前，如果想撤销对某个文件的修改，可以右键点击它，选择 `Git -> Revert` 或 `Rollback`。
*   **快捷键：** 熟练使用快捷键（如 `Ctrl+K` 提交，`Ctrl+T` 拉取）可以极大提高效率。

---

