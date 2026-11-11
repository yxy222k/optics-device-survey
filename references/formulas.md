# 常用推导式库

按块收录。每块：适用对象 / 式子 / 口径 / 典型数值。公式只用 KaTeX 子集（落盘文档用）；聊天回复里按纯文本转写。用到哪块引用哪块，推导时把口径（单程/双程、格距/加窗 FWHM、空气/介质）写在报告里。**公式与系数只住这里**，清单与基线只指向，不复制。

本库波数 $\sigma = 1/\lambda$（不是角波数 $k = 2\pi/\lambda$；若用 $k$，各式分母补 $2\pi$）。

## A. 谱域干涉 / FFT 深度谱

适用：选光谱仪、定光源带宽、判断"换像素数 / 换波段能否改善"。

- FFT 格距（Rayleigh 判据，单程光程口径）：
  $$\delta z_{\text{bin}} = \frac{1}{2\,\Delta\sigma},\qquad \Delta\sigma = \frac{1}{\lambda_{\min}} - \frac{1}{\lambda_{\max}}$$
  峰 FWHM（幅度谱）：矩形窗 ≈ 1.21 格，Hann ≈ 2.0 格。算例 500–700 nm：格距 $0.875\,\mu\mathrm{m}$ → Hann FWHM ≈ 2 格 $= 1.75\,\mu\mathrm{m}$。报告里写"分辨率"时注明是格距还是 Hann FWHM；反推带宽需求时两档差 2 倍。
- 奈奎斯特深度上限（单程）：
  $$z_{\max} \sim \frac{1}{4\,\delta\sigma},\qquad \delta\sigma \approx \frac{\delta\lambda}{\lambda_{\min}^{2}}\ \Rightarrow\ z_{\max}\approx\frac{\lambda_{\min}^{2}}{4\,\delta\lambda}$$
  $\delta\lambda$ 是**探测器物理像素间距**（在 $\lambda_{\min}$ 处取值）；重采样点数与补零只改显示网格，不改上限。蓝端条纹最密、最先撞奈奎斯特，所以上限由 $\lambda_{\min}$ 定；砍蓝端按 $\lambda_{\min}^2$ 换上限，代价是分辨率——两者是跷跷板。贴极限时峰质量差，工程上按 3 像素/周期打 2/3 折。超限后果不是"没峰"而是混叠出位置错误的假峰。
- 介质内：$\delta z$、$z_{\max}$、$d_{1/2}$ 都再除群折射率 $n_g$（随分析截取的波段变化，不是相折射率）。
- 光学分辨率 $\delta\lambda_{\mathrm{opt}}$（狭缝 + 像差，通常是采样间隔的 2–3 倍）**不改峰宽**，而是把深度谱乘一个随深度衰减的包络（仪器函数在 $\sigma$ 域的卷积）。高斯仪器函数、$\delta\lambda_{\mathrm{opt}}$ 取 **FWHM**（厂家给 $1/e^2$ 宽度时 ×0.589）时幅值减半深度（单程）：
  $$d_{1/2} \approx \frac{\ln 2}{\pi}\,\frac{\lambda^{2}}{\delta\lambda_{\mathrm{opt}}}$$
  600 nm、1 nm → $\approx 80\,\mu\mathrm{m}$。"幅值减半"= −6 dB；幅度按 20·log、功率按 10·log 得到的是同一个 dB 数，所以文献的"6 dB 滚降深度"就是这个 $d_{1/2}$；若文献报的是 **3 dB（半功率）滚降**，深度为 $0.71\,d_{1/2}$。实际可用量程取"奈奎斯特 × 采样裕量"与 $d_{1/2}$ 两者中的小者。
- 条纹周期与像素积分：$\Delta\lambda_{\text{条纹}} = \lambda^{2}/(2\,n_g\,d)$，每条纹采样点数 $m = \Delta\lambda_{\text{条纹}}/\delta\lambda$；像素积分使条纹幅值乘
  $$\frac{\sin(\pi/m)}{\pi/m}\quad(m=2\ \text{奈奎斯特}\ 0.64,\ m=3\ 0.83,\ m=4\ 0.90)$$
  与 $d_{1/2}$ 包络相乘——这就是"像素数翻倍改善多少"的量。
- **像素数本身不是参数**：$\delta\lambda = $ 光谱仪**全量程**跨度 / 像素数（物理采样间隔，不是分析截取段除以像素数，也不是重采样网格）。先问新机型的波段跨度与像元间距：同跨度半像元 → 改采样间隔（奈奎斯特上限与像素积分因子）；同像元倍跨度 → 改的是带宽（峰宽），奈奎斯特不动；两种都不改由狭缝决定的 $\delta\lambda_{\mathrm{opt}}$。

## B. 辐射度学（光源、光纤、照明）

适用：比较光源、判断"大功率大芯径"是不是账面数字。

- 光展量 $G = A\,\Omega_{\text{proj}} = \pi (d/2)^{2}\cdot\pi\,\mathrm{NA}^{2}$（$\Omega$ 取投影立体角 $\pi\sin^2\theta$，对任意 NA 严格；介质中 NA 已含 $n$，不再另乘 $n^2$；不要用几何立体角 $2\pi(1-\cos\theta)$）。无源系统中 $G$ 不会减小，亮度 $L$ 只减不增。
- 工作段谱辐亮度（单位 $\mathrm{mW\,mm^{-2}\,sr^{-1}\,nm^{-1}}$）：
  $$L_\lambda = \frac{\varphi\,P}{G\,\Delta\lambda_{\text{工作段}}}$$
  $\varphi$ 为工作段内能量占比（从光谱曲线读，✅；读不到按假设谱形，🧮），分母是**工作段宽度**，不是 datasheet 功率的积分带；积分带只用来核对 $P$ 与曲线自洽。算例：120 mW @ Ø1000 μm / NA 0.22（$G = 0.119\,\mathrm{mm^2\,sr}$），500–700 nm 内 $\varphi = 10\%$ → $12/(0.119\times200) = 0.50$；若误用积分带 1980 nm 作分母会得 0.05，偏小 10 倍。
- 能送进受限光斑的功率：$P_{\text{进光斑}} = L_\lambda\cdot G_{\text{系统}}\cdot\Delta\lambda_{\text{处理段}}$。$G_{\text{系统}}$ 由**样品面**光斑面积与**同一侧**的 NA 定——面积与立体角必须取同一空间（数值见各项目基线）。
- 源尺寸决定亮度上限：弧隙 / 等离子体尺寸 / 发光面积差一个量级，亮度差两个量级。
- **口径协议**（光源归一化时照此写进报告）：① 统一报工作波段处的 $L_\lambda$，量纲与需求卡首要指标一致，不用全谱总量互比；② 先做同条件直接比（同芯径同 NA 的实测值），再反算；③ 用"X mW @ Ø d / NA"反算时取**芯径最小**的那一行（最接近系统 étendue），注明这是下界；厂家直接给的源 radiance 优先用；厂家自己发布的"归一化表"仍是厂家工作点，只作旁证；④ 两种口径都给并指明哪种对应工作点，最后折到 $P_{\text{进光斑}}$。

## C. 单模光纤 + 高斯光束（测头、准直器、聚焦镜）

适用：1310/1550 nm 光纤共路测头的光斑、工作距、摆放窗、角度容差。

- 光纤模场半径 $w_f = \mathrm{MFD}/2$（SMF-28 @1550：MFD 10.4 μm），远场发散半角 $\theta = \lambda/(\pi w_f)$。
- 准直束径 $2w_c = 2 f_1\theta$；聚焦有效 NA $\mathrm{NA}_{\text{eff}} = w_c/f_2$；物方光斑 $2w_0 = \mathrm{MFD}\cdot f_2/f_1$；瑞利长度 $z_R = \pi w_0^{2}/\lambda$。
- 反射式共路、平面反射面离焦 $\Delta z$ 的回耦效率（已含往返）：
  $$\eta(\Delta z) = \frac{1}{1+(\Delta z/z_R)^{2}}$$
- 反射面倾角 $\theta$ 的回耦（回程光束偏 $2\theta$）：
  $$\eta(\theta) = \exp\!\left[-\left(\frac{2\theta}{\theta_d}\right)^{2}\right],\qquad \theta_d = \frac{\lambda}{\pi w_0},\qquad \theta_{1/2}\approx 0.42\,\theta_d$$
  角度容差与摆放窗对 $f_1$ 反向拉扯（$\theta_d\propto\mathrm{NA}_{\text{eff}}$，$z_R\propto 1/\mathrm{NA}_{\text{eff}}^{2}$），没有全赢解。
- 镜片标称 NA 与系统 $\mathrm{NA}_{\text{eff}}$ 无关；按标称 NA 排名会完全反序。
- 带内色焦移经验判据：$\Delta f \le z_R/6$（对应 $\eta\ge 0.97$）时单片非球面够用，不必上消色差胶合。
- APC 端面出射偏折 $\approx (n-1)\cdot 8^\circ \approx 3.7^\circ$，适配板 / 准直器要选带角度补偿的版本。

## D. 寄生腔与固定峰

适用：任何插进光路的新件（窗口、分束器、衰减器、保护玻璃、光纤接头）。

三问：① 新件带来哪几对反射面？② 每对的单程群光程 $n_g t$（或空气隙 $t$）是多少？③ 落不落在分析窗 $[0, z_{\max}]$ 内？落在窗内就会变成固定峰，与样品无关。
- 菲涅尔反射 $R = \left(\frac{n-1}{n+1}\right)^{2}$：玻璃 ≈4%，Si @1310 nm（$n\approx3.5$）≈31%。
- 消法：楔角 $\alpha$ 把两面反射拉开 $\approx 2n\alpha$，再配合整体倾角让两者都落在接收孔径外（单靠楔角只能消一面）；把厚度做大使峰落在窗外；单面抛光把背面散掉。作参考面的表面**不能**镀 AR。
- 扫描过程中寄生峰与信号峰交叉处会出现局部假非线性——设计时让两者在整个量程内不相遇。

## E. dB、透过率与双程口径

- $T = 10^{-A/10}$。反射式共路光双程穿过在线器件：有效衰减 $2A$；对接损耗按**净新增对接面数 × 2（往返）**计，净新增 = 器件接头数 − 它替换掉的原有对接点数（插在原本就有一个接头的位置净增 1 面 → 往返 2 次；插在连续光纤中间净增 2 面 → 4 次），每次 ≈0.2–0.5 dB。带接头器件的标称插损通常已含自身两端对接，别重复计。厂家标称衰减是单程透射法测的。
- 回损 $\mathrm{RL}$ dB → 反射功率比 $10^{-\mathrm{RL}/10}$（60 dB → $10^{-6}$）；按 D 块判断这个反射会不会成峰。

## F. 误差预算（标定装置、位移溯源）

- Abbe 误差 $\varepsilon = L_{\text{离轴}}\cdot\theta_{\text{俯仰}}$：$25\,\mathrm{mm}\times5\,\mu\mathrm{rad} = 125\,\mathrm{nm}$——单头不可接受，对称双头取平均后一阶抵消。
- 热胀 $\Delta L = \alpha L\,\Delta T$：铝 23、碳钢 11.5、不锈钢 304 ≈17、熔石英 0.5、殷钢 1.2 ppm/K。50 mm 钢环路 1 K → $0.6\,\mu\mathrm{m}$。测量环路越短越好；差分测量把框架漂移变成共模。
- 空气折射率：$\mathrm{d}n/\mathrm{d}T \approx -1\times10^{-6}$/K，$\mathrm{d}n/\mathrm{d}P \approx +2.7\times10^{-9}$/Pa（10 hPa ≈ 3 K 等效），$\mathrm{d}n/\mathrm{d}\mathrm{RH}\approx-1\times10^{-8}$/%RH；三项都乘死程长度。
- 各项独立时 RSS 合成；目标"溯源不确定度 ≪ 被测仪器指标"（通常取 1/10）。

## G. 成像与采样

- Abbe 极限 $d = \lambda/(2\,\mathrm{NA})$；有效像元 $p_{\text{eff}} = p/M \lesssim d/2$（奈奎斯特）。
- 真正封顶的常常是系统 LSF/MTF（像差、离焦、杂散光），不是像素也不是衍射极限；找 MTF 曲线，找不到标 ❓。

## H. Planck 与空芯波导损耗

- 黑体谱密度比（比较热光源在某波长的亮度）：$B(\lambda,T)\propto \lambda^{-5}\left(e^{c_2/\lambda T}-1\right)^{-1}$，$c_2 = 14388\ \mu\mathrm{m\cdot K}$。卤钨灯（~2800 K）在 500 nm 比 ~6000 K 等效色温的弧光源低约两个量级。
- 深孔里光走硅壁空芯波导漏泄模（Marcatili–Schmeltzer，EH11）：
  $$\alpha \approx \left(\frac{u_{11}}{2\pi}\right)^{2}\frac{\lambda^{2}}{a^{3}}\,K(n),\qquad u_{11}=2.405,\quad K(n)=\frac{n^{2}+1}{2\sqrt{n^{2}-1}}$$
  $a$ 为孔**半径**（$d = 2a$），$\alpha$ 为**功率**衰减系数（Np/μm）。孔底峰看到的是往返 $2h$：功率透过率 $T = e^{-2\alpha h}$，幅度谱（20·log）中按场因子 $e^{-\alpha h}$ 计，即 dB = $10\log_{10}T$。算例：$d = 3\,\mu\mathrm{m}$、$\lambda = 0.6\,\mu\mathrm{m}$、硅壁 $n = 3.9$ → $\alpha\approx0.034\,\mu\mathrm{m}^{-1}$，$h = 16\,\mu\mathrm{m}$ 往返 ≈ −4.7 dB。损耗 $\propto \lambda^{2}/a^{3}$：孔径减半损耗 ×8，波长减半损耗 ÷4。
- 高斯光斑内孔占比 $f = 1-\exp\!\left[-2(d/\varnothing_{\text{光斑}})^{2}\right]$，$\varnothing_{\text{光斑}}$ 取 $1/e^{2}$ 强度直径（FWHM ×1.70 折成 $1/e^2$）。非纯高斯光斑（如 Zemax 孪生）本式会偏高，以项目孪生值为准（数值见项目基线）；外推小孔时把本式（上界）与几何占比 $(d/\varnothing)^2$（下界）都列出，SNR 预估按下界做 go/no-go——该上下界关系只在 $d/\varnothing \lesssim 0.9$ 成立，接近交叉点时两式差 <3%，直接用项目孪生值。孔底峰幅度 $\propto f$，桥峰 $\propto \sqrt{f(1-f)}$。
