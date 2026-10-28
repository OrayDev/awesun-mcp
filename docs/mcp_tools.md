# AweSun MCP 工具文档

AweSun MCP Server 提供向日葵客户端操作能力，包含设备管理、远控会话、桌面操作和远程文件四大类工具。

---

## 一、设备管理类工具

### 1. device_add
**功能**：将新设备添加到设备列表中，可设置设备名称和描述便于管理。添加成功后可通过 device_search 查询该设备。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| name | string | 是 | 设备的名称 |
| desc | string | 否 | 设备的描述信息 |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| remote_id | string | 设备ID |

---

### 2. device_search
**功能**：根据关键词模糊搜索设备列表中的设备，支持按设备名称检索，返回匹配的设备基本信息列表。常用于查找特定设备以进行后续远控操作。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| keyword | string | 否 | 查询关键字，支持模糊检索 |
| limit | int64 | 是 | 查询结果数量限制，最大值为100 |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| total | int64 | 设备总数 |
| devices | array | 设备列表 |

**设备对象字段**：
- remote_id: 设备ID
- name: 设备名称
- description: 设备备注
- pc_name: 计算机名称
- cpu: CPU型号
- mac: MAC地址
- memory: 内存信息
- screen_size: 屏幕分辨率
- client: 客户端类型标识
- os: 操作系统类型
- os_name: 操作系统名称
- base_board: 主板型号
- disk_drive: 硬盘信息
- video_controller: 显卡信息
- network_adapter: 网卡信息
- version: 客户端版本
- online: 是否在线
- fastcode: 远控识别码/快码
- login_time: 登录时间
- ip: 公网IP地址
- lan_ip: 局域网IP地址
- create_time: 创建时间

---

### 3. device_info
**功能**：查询指定设备的完整详细信息，包括硬件配置（CPU、内存、硬盘、显卡）、网络信息（IP地址、MAC地址）、系统版本、在线状态、支持的插件等。必须先通过 device_search 获取设备ID。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| remote_id | int64 | 是 | 设备ID |

**输出结果**：
- remote_id: 设备ID
- name: 设备名称
- description: 设备备注
- pc_name: 计算机名称
- cpu: CPU型号
- mac: MAC地址
- memory: 内存信息
- screen_size: 屏幕分辨率
- client: 客户端类型标识
- os: 操作系统类型
- os_name: 操作系统名称
- base_board: 主板型号
- disk_drive: 硬盘信息
- video_controller: 显卡信息
- network_adapter: 网卡信息
- version: 客户端版本
- online: 是否在线
- fastcode: 远控识别码/快码
- login_time: 登录时间
- ip: 公网IP地址
- lan_ip: 局域网IP地址
- plugins: 支持的远控插件列表
- create_time: 创建时间

---

### 4. device_update
**功能**：修改指定设备的名称和描述信息，用于更新设备列表中的显示名称和备注，便于设备管理和识别。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| remote_id | int64 | 是 | 设备ID |
| name | string | 否 | 设备的名称 |
| desc | string | 否 | 设备的描述信息 |

**输出结果**：操作成功/失败信息

---

### 5. device_remove
**功能**：从设备列表中删除指定的设备，仅移除列表记录不会影响被控端软件，删除后无法恢复。删除前建议确认设备已不再需要使用。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| remote_id | int64 | 是 | 设备ID |

**输出结果**：操作成功/失败信息

---

### 6. device_wakeup
**功能**：向绑定了开机硬件的设备发送开机指令，需要设备端配置开机棒或主板支持WOL功能。指令下发后设备将在1-2分钟内完成开机并上线。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| remote_id | int64 | 是 | 设备ID |

**输出结果**：下发开机指令成功提示，设备上线大约需要耗费1-2分钟

---

### 7. device_shutdown
**功能**：向在线的远程设备发送关机指令，设备需处于在线状态且被控端支持关机功能。指令下发后设备将在1-2分钟内完成关机并离线。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| remote_id | int64 | 是 | 设备ID |

**输出结果**：下发关机指令成功提示，设备离线大约需要耗费1-2分钟

---

## 二、远控会话类工具

### 8. control_connect
**功能**：发起与指定设备的远控会话连接，支持远程文件(file)、远程桌面(desktop)、远程CMD(cmd2)、远程SSH(ssh)、桌面观看(desktop_view)、远程摄像头(newcamera)、端口转发(forward)。连接成功后返回会话ID，用于后续的桌面操作、截图、命令执行等。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| type | string | 是 | 远控类型：file(远程文件)、desktop(远程桌面)、cmd2(远程CMD/Windows)、ssh(远程SSH/Linux/Mac)、desktop_view(桌面观看)、newcamera(摄像头)、forward(端口转发) |
| remote_id | int64 | 是 | 被控设备ID（通过设备列表已存在的设备发起远控） |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| session_id | string | 远控会话ID |

---

### 9. control_connect_state
**功能**：查询指定远控会话的连接状态，适用于 `control_connect` 工具建立连接后，需要用户手动输入验证码的场景，调用此工具验证远程的连接状态。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| type | string | 远控类型 |
| session_id | string | 远控会话ID |
| state | string | 远控会话状态 |
| message | string | 描述信息 |

---

### 10. control_sessions
**功能**：查询所有当前活跃的远控会话，包括会话ID/会话类型和状态。获取的会话ID可用于截图、执行命令、桌面操作、断开连接等后续操作。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| type | string | 否 | 远控类型过滤，不传则返回所有远控会话。可选值：file、desktop、cmd2、ssh、desktop_view、newcamera、forward |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| total | int64 | 总条数 |
| message | string | 服务器返回的消息 |
| sessions | array | 远控会话列表 |

**会话对象字段**：
- type: 远控类型
- session_id: 远控会话ID
- remote_id: 被控设备ID
- fastcode: 被控设备快码

---

### 11. control_disconnect
**功能**：终止指定的活跃远控会话，立即断开与该会话的连接。断开后如需再次操作需要重新调用 control_connect 建立连接。建议在不使用会话时及时断开以释放资源。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID |

**输出结果**：操作成功/失败信息

---

### 12. control_command
**功能**：在已建立的CMD远程会话中执行命令，目前支持Windows的CMD。返回命令的退出码、标准输出和错误输出。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持cmd2类型的远程会话） |
| command | string | 是 | 要执行的命令 |
| args | array | 否 | 命令参数数组 |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| exit_code | int64 | 退出码 |
| std_out | string | 标准输出流内容 |
| std_err | string | 标准错误流内容 |

---

### 13. control_screenshot
**功能**：对指定的远程桌面会话(desktop/desktop_view)进行截图，返回Base64编码的图片数据及尺寸信息。截图可用于获取远程设备的当前画面状态，辅助判断操作结果。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop/desktop_view类型的远程会话） |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| image_path | string | 截图保存位置 |
| image_width | int64 | 图片宽度 |
| image_height | int64 | 图片高度 |

---

### 14. control_portforward
**功能**：在已建立的端口转发远程会话中配置端口转发规则（覆盖），需先建立有效的端口转发远程会话，用于实现本地与远程主机端口的双向数据转发。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持forward类型的远程会话） |
| target_addresses | array | 是 | 内网目标主机的IP或主机名:协议端口（仅支持TCP协议，支持同时配置多个，如：['127.0.0.1:22', '192.168.10.1:3389']） |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| target_address | string | 目标地址 |
| local_address | string | 本地地址(通过本地地址来转发数据) |

---

## 三、桌面操作类工具

### 15. desktop_click_mouse
**功能**：在远程桌面会话中模拟鼠标点击操作，支持左键、右键、中键点击及双击。坐标需使用归一化值(0.0-1.0)，通过 x_pixel/屏幕宽度 计算。适用于按钮点击、菜单选择等场景。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| coordinates | array | 是 | 需要点击的坐标[x,y]，需要归一化，取值范围在0.0到1.0之间，公式为 x = x_pixel / width, y = y_pixel / height |
| button | string | 是 | 点击鼠标按钮类型：left、right、middle |
| clicks | int64 | 是 | 点击次数，传2则为双击 |

**输出结果**：操作成功/失败信息

---

### 16. desktop_move_mouse
**功能**：将鼠标光标移动到远程桌面的指定坐标位置，坐标需归一化(0.0-1.0)。常用于拖拽操作前的定位、悬停触发菜单、或配合截图确定点击位置。仅移动不触发点击。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| coordinates | array | 是 | 目标坐标[x,y]，需要归一化，取值范围在0.0到1.0之间 |

**输出结果**：操作成功/失败信息

---

### 17. desktop_drag_mouse
**功能**：在远程桌面中模拟鼠标拖拽操作，支持按住指定按键(left/right/middle)沿路径移动。可用于文件拖拽、窗口调整大小、选择文本区域等。路径坐标需归一化(0.0-1.0)。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| paths | array | 是 | 拖拽路径，每个坐标一组，格式['x,y','x,y']，需要归一化，取值范围在0.0到1.0之间 |
| button | string | 是 | 拖拽鼠标按钮类型：left、right、middle |
| hold_keys | array | 否 | 拖拽鼠标时按下的键：shift、ctrl、alt等 |

**输出结果**：操作成功/失败信息

---

### 18. desktop_scroll_mouse
**功能**：在远程桌面的指定位置模拟鼠标滚轮滚动，支持向上(up)或向下(down)滚动指定次数。用于滚动网页、文档、列表等内容查看。坐标需归一化(0.0-1.0)。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| coordinates | array | 是 | 坐标[x,y]，需要归一化，取值范围在0.0到1.0之间 |
| direction | string | 是 | 滚动方向：up、down |
| scroll_count | int64 | 是 | 滚动次数 |

**输出结果**：操作成功/失败信息

---

### 19. desktop_press_keys
**功能**：在远程桌面中精确控制按键的按下或释放操作，适合需要精细控制按键状态的场景。支持单独按下(down)、单独释放(up)，或不指定时自动按下后释放。可用于长按、连击等复杂操作。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| keys | array | 是 | 按键序列，如control、alt、v、return |
| press | string | 否 | 按键操作：up/down（不填则默认为先按下后释放，如果传入down，则一定要再调用一次up） |

**输出结果**：操作成功/失败信息

---

### 20. desktop_typing_keys
**功能**：在远程桌面中执行组合快捷键操作，如复制(Ctrl+C)、粘贴(Ctrl+V)、保存(Ctrl+S)等。按顺序按下所有按键，延迟后再按相反顺序释放。适合需要同时按住多个键的场景。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| keys | array | 是 | 按顺序键入的键名称数组，如control、shift、win、alt、return |
| delay | int64 | 否 | 按下和释放按键之间的延迟（以毫秒为单位） |

**输出结果**：操作成功/失败信息

---

### 21. desktop_typing_text
**功能**：在远程桌面中逐字符模拟键盘输入文本，适合输入短文本内容。输入前需确保输入框已获取焦点。可设置字符间延迟(毫秒)控制输入速度。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| text | string | 是 | 需要输入的文本字符串 |
| delay | int64 | 否 | 字符输入间隔，单位毫秒 |

**输出结果**：操作成功/失败信息

---

### 22. desktop_paste_text
**功能**：在远程桌面中通过系统剪贴板粘贴长文本内容，比逐字符输入更高效。适用于输入大段文本、代码、命令等场景。使用前需确保输入框已获取焦点，且被控端支持剪贴板同步。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持desktop类型的远程会话） |
| text | string | 是 | 需要粘贴的文本字符串 |

**输出结果**：操作成功/失败信息

---

### 23. desktop_waiting
**功能**：在远控操作序列中插入暂停等待，用于在关键操作后等待系统响应或页面加载完成。指定持续时间(毫秒)后自动继续执行后续工具。建议在网络延迟或UI渲染场景中使用。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| duration | int64 | 是 | 暂停时间单位：毫秒（UI 渲染等待建议：≤500ms，避免过长等待） |

**输出结果**：操作成功信息

---

## 四、远程文件类工具

### 24. control_file_list
**功能**：在远程文件会话中列出远程主机文件列表，支持指定路径、关键字过滤和分页限制。返回文件/文件夹的详细信息包括名称、大小、类型、权限、创建/修改时间等。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持file类型的远控会话） |
| path | string | 否 | 指定父级路径，相当于先CD到该目录（如果不指定则默认用户目录） |
| keyword | string | 否 | 文件名关键字，用于过滤文件列表 |
| limit | int64 | 否 | 文件列表限制，最大100 |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| total | int64 | 文件总数 |
| files | array | 文件列表 |

**文件对象字段**：
- type: 文件类型（folder/file）
- name: 文件名
- size: 文件大小（Byte）
- created: 文件创建时间
- modified: 文件修改时间
- permission: 文件权限
- owner: 文件所有者
- group: 文件所属组
- path: 文件全路径
- mime_type: 文件MIME类型

---

### 25. control_file_mkdir
**功能**：在远程文件会话中创建指定的文件夹，需在已建立的远程文件会话中使用。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持file类型的远控会话） |
| path | string | 是 | 指定父级路径，相当于先CD到该目录 |
| name | string | 是 | 文件夹名称 |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| type | string | 文件类型（folder/file） |
| name | string | 文件夹名称 |
| path | string | 文件夹全路径 |
| created | int64 | 文件夹创建时间 |

---

### 26. control_file_remove
**功能**：在远程文件会话中删除指定的文件或文件夹，删除文件夹时可选择是否递归删除子目录。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持file类型的远控会话） |
| path | string | 是 | 要删除的文件/文件夹路径 |
| recursive | bool | 否 | 删除文件夹时是否递归删除 |

**输出结果**：操作成功/失败信息

---

### 27. control_file_rename
**功能**：在远程文件会话中重命名指定的文件或文件夹。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持file类型的远控会话） |
| path | string | 是 | 原文件/文件夹路径 |
| new_name | string | 是 | 新名称（仅名称，不含路径） |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| name | string | 文件名 |
| old_path | string | 原文件路径 |
| new_path | string | 新文件路径 |

---

### 28. control_file_transfer
**功能**：在远程文件会话中创建文件或文件夹传输任务，支持双向传输。down表示从远程下载到本地，up表示从本地上传到远程。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持file类型的远控会话） |
| transfer_type | string | 是 | 传输类型：down（下载）/up（上传） |
| local_path | string | 是 | 本地文件/文件夹路径 |
| remote_path | string | 是 | 远程文件/文件夹路径 |
| override | bool | 是 | 是否覆盖远程文件 |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| transfer_id | string | 传输任务ID |

---

### 29. control_file_transfer_cancel
**功能**：在远程文件会话中取消正在进行中的文件或文件夹传输任务，可指定任务ID取消特定任务，不指定则取消当前会话的所有传输任务。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持file类型的远控会话） |
| transfer_id | string | 否 | 要取消的文件传输任务ID，不传则取消当前会话所有任务 |

**输出结果**：操作成功/失败信息

---

### 30. control_file_transfer_state
**功能**：在远程文件会话中查询进行中文件或文件夹传输任务的进度，包括传输速度、已传输大小、剩余时间、进度百分比等详细信息。

**输入参数**：

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| session_id | string | 是 | 远控会话ID（仅支持file类型的远控会话） |
| transfer_id | string | 否 | 筛选文件传输任务的ID，不传则返回当前会话所有任务 |

**输出结果**：

| 参数 | 类型 | 说明 |
|---|---|---|
| total | int64 | 传输任务总数 |
| transfers | array | 传输任务列表 |

**传输任务对象字段**：
- session_id: 远控会话ID
- transfer_id: 传输任务ID
- transfer_type: 传输类型
- transfer_status: 传输状态
- local_path: 本地文件/文件夹路径
- remote_path: 远程文件/文件夹路径
- total_bytes: 文件/文件夹总大小（Byte）
- speed_bytes: 传输速度（Byte/s）
- transferred_bytes: 已传输大小（Byte）
- remaining_seconds: 剩余时间（秒）
- progress_percent: 传输进度百分比

---

## 附录

### 坐标归一化说明

在桌面操作工具中，涉及到坐标的参数都需要进行归一化处理：

- 取值范围：0.0 到 1.0 之间
- 转换公式：`x = x_pixel / width`, `y = y_pixel / height`
- 例如：在 1920x1080 的屏幕上，坐标 (960, 540) 的归一化值为 [0.5, 0.5]

### 远控类型说明

| 类型值 | 说明 |
|---|---|
| file | 远程文件 |
| desktop | 远程桌面（支持鼠标和键盘操作） |
| cmd2 | 远程CMD（适用于Windows系统） |
| ssh | 远程SSH（适用于Linux/Mac系统） |
| desktop_view | 桌面观看（仅支持截图） |
| newcamera | 摄像头（仅支持截图） |
| forward | 端口转发 |

