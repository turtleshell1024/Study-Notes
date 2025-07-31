# 软件下载安装

## 下载安装包

http://circos.ca/software/download/
```bash
tar xvfz circos-0.69-9.tgz
cd circos-0.69-9
cd bin
./list.modules
```

## 检测未安装的模组

**circos -modules**

```
ok       1.26 Carp
missing            Clone
missing            Config::General
ok       3.40 Cwd
ok      2.145 Data::Dumper
ok       2.52 Digest::MD5
ok       2.84 File::Basename
ok       3.40 File::Spec::Functions
ok     0.2301 File::Temp
ok       1.51 FindBin
missing            Font::TTF::Font
missing            GD
missing            GD::Polyline
ok        2.4 Getopt::Long
ok       1.16 IO::File
missing            List::MoreUtils
ok       1.27 List::Util
missing            Math::Bezier
ok      1.997 Math::BigFloat
missing            Math::Round
missing            Math::VecStat
ok       1.02 Memoize
ok       1.30 POSIX
missing            Params::Validate
ok       1.63 Pod::Usage
missing            Readonly
missing            Regexp::Common
missing            SVG
missing            Set::IntSpan
missing            Statistics::Basic
ok       2.45 Storable
ok       1.16 Sys::Hostname
ok       2.02 Text::Balanced
missing            Text::Format
ok     1.9725 Time::HiRes
```

## 模组安装

cpanm 是一个更简单、更快速的 Perl 模块安装工具。你可以通过 local::lib 来安装 cpanm，然后使用它来安装模块。

步骤 1：安装 cpanm
在没有管理员权限的情况下，首先安装 cpanm：
```
curl -L https://cpanmin.us | perl - --self-upgrade
```

步骤 2：使用 cpanm 安装模块
安装 cpanm 后，使用它来安装任何模块。例如，安装 Clone 模块：

```
cpanm Clone
cpanm Config::General
cpanm --notest Font::TTF::Font
cpanm List::MoreUtils
cpanm Math::Bezier
cpanm Math::Round
cpanm Math::VecStat
cpanm Params::Validate
cpanm Readonly
cpanm Regexp::Common
cpanm SVG
cpanm Set::IntSpan
cpanm Statistics::Basic
cpanm Text::Format
cpanm GD
cpanm GD::Polyline
```

cpanm 会自动将模块安装到你自己的用户目录中。

## 再次检查模组

**circos -modules**
```

ok       1.26 Carp
ok       0.47 Clone
ok       2.65 Config::General
ok       3.40 Cwd
ok      2.145 Data::Dumper
ok       2.52 Digest::MD5
ok       2.84 File::Basename
ok       3.40 File::Spec::Functions
ok     0.2301 File::Temp
ok       1.51 FindBin
ok       0.39 Font::TTF::Font
ok       2.83 GD
ok        0.2 GD::Polyline
ok        2.4 Getopt::Long
ok       1.16 IO::File
ok      0.430 List::MoreUtils
ok       1.27 List::Util
ok       0.01 Math::Bezier
ok      1.997 Math::BigFloat
ok       0.08 Math::Round
ok       0.08 Math::VecStat
ok       1.02 Memoize
ok       1.30 POSIX
ok       1.31 Params::Validate
ok       1.63 Pod::Usage
ok       2.05 Readonly
ok 2024080801 Regexp::Common
ok       2.87 SVG
ok       1.19 Set::IntSpan
ok     1.6611 Statistics::Basic
ok       2.45 Storable
ok       1.16 Sys::Hostname
ok       2.02 Text::Balanced
ok       0.63 Text::Format
ok     1.9725 Time::HiRes
```

## 软件测试

```bash
cd software/circos/circos-0.69-9/example
./run
```

# 准备文件

## 染色体文件：karyotype_ZJU1.0.txt 

```R
fai = read.table('../refdata/GCF_015476345.1_ZJU1.0_genomic.fna.fai',header = F)
fai = fai[c(1:33),]
karyotype = data.frame(chr='chr',V2='-',ID=paste0('chr',fai$V1),Label=paste0(fai$V1),sta=0,end=fai$V2,col=c('220,159,70','157,183,165','214,225,194','251,177,162','109,120,118','136,20,0','129,89,137','182,201,184','74,109,66','58,87,121','220,159,70','157,183,165','214,225,194','251,177,162','109,120,118','136,20,0','129,89,137','182,201,184','74,109,66','58,87,121','220,159,70','157,183,165','214,225,194','251,177,162','109,120,118','136,20,0','129,89,137','182,201,184','74,109,66','58,87,121','220,159,70','157,183,165','214,225,194'))

write.table(karyotype,'data/karyotype_ZJU1.0.txt',quote = F,col.names = F,row.names = F,sep = '\t')
```
```
chr	-	chr1	1	0	207238429	220,159,70
chr	-	chr2	2	0	164862000	157,183,165
chr	-	chr3	3	0	120086012	214,225,194
chr	-	chr4	4	0	76269206	251,177,162
chr	-	chr5	5	0	66856311	109,120,118
chr	-	chr6	6	0	37939480	136,20,0
chr	-	chr7	7	0	39896445	129,89,137
```
## 位点密度文件：SNPdensity.txt

```R
plink --bfile ../finalqc --chr-set 39 --chr 1-39 Z --allow-extra-chr --extract sites.id --recode-vcf --make-bed --out data/loci_60k
vcftools --vcf data/loci_60k.vcf --SNPdensity 1000000 --out data/SNPdensity

SNPdensity = read.table('data/SNPdensity.snpden',header = T)
chr = unique(SNPdensity$CHROM)
n= length(chr)
result = data.frame()
for(i in 1:n){
  temp = SNPdensity[SNPdensity$CHROM == chr[i],]
  temp$CHROM = paste0('chr',temp$CHROM)
  if(length(temp$BIN_START) > 1){
    end = temp$BIN_START[2:length(temp$BIN_START)]
    end = c(end,temp$BIN_START[length(temp$BIN_START)]+1000000)
    temp$end = end
  }else{
    temp$end = temp$BIN_START + 1000000
  }
  temp = temp[,c(1,2,5,3)]
  result = rbind(result,temp)
}

write.table(result,'data/SNPdensity.txt',quote = F,col.names = F,row.names = F,sep = '\t')

```

```
chr1	0	1000000	50
chr1	1000000	2000000	58
chr1	2000000	3000000	58
chr1	3000000	4000000	60
chr1	4000000	5000000	58
chr1	5000000	6000000	60
chr1	6000000	7000000	59
chr1	7000000	8000000	55
chr1	8000000	9000000	58
chr1	9000000	10000000	60
chr1	10000000	11000000	58
chr1	11000000	12000000	59
chr1	12000000	13000000	59
```

## 配置文件：circos.conf

```
#指定染色体文件（绝对/相对路径+文件名）
karyotype = ./data/karyotype_ZJU1.0.txt  
chromosomes_units = 1000000  #定义最小单位，即100万bp为一个units，即1u = 100w，后面刻度线都是基于此的操作，如果染色体长度都在5kw以上，推荐用100w，否则推荐用10w
chromosomes_display_default = yes
#染色体环-----------------------------------------------------------------------------------
<ideogram>                #这是定义染色体相关参数的标签，以</ideogram>结尾，其中包括要设置的参数
<spacing>                 #定义染色体间隙宽度的标签，以</spacing>，其中包括要设置的参数
default = 0.005r          #r指的是圆的周长，设置0.5%圆的周长为间隙
#<pairwise hsY;hs1>       #可以用<pairwise>标签特别指定某些染色体的间隙（用的是ID），因为在大多数文章中，都会留一个大间隙，来放label
#spacing = 20r            #这里20r表示是相对default = 0.005r的20倍，也就是10%的圆的周长
#</pairwise>              #标签都要以</>结尾，
</spacing>                #间隙定义结束，下面是对染色体样式的调整
radius           = 0.90r  #轮廓的位置，这里的r指的是半径，由圆心到圆周上范围依次是0-1r，，超出部分将不再显示。
thickness        = 50p    #染色体整体的宽度，这里p指的是像素大小，也可以用r表示，1r=1500p
fill             = yes    #是否为染色体填充颜色，如果为yes，自动用第七列定义的颜色着色
stroke_color     = dgrey  #染色体边框的颜色，支持多种格式的输入，如：red或255,182,106
stroke_thickness = 2p     #染色体边框的粗细
show_label       = yes        #选择yes表示要显示label
label_font       = default    # 字体可以再 etc/fonts.conf 查看所有，默认为CMUBright-Roman
label_radius     = dims(image,radius)-60p  #使用dims()获取图像半径的大小，从而定位染色体标签的位置，也可以直接定义1.075r
label_size       = 35         #字体的大小
label_parallel   = yes        #将Label的方向设置为与染色体平行
</ideogram>               #定义染色体属性的标签结束

#刻度-----------------------------------------------------------------------------------
show_ticks          = yes  #选择yes表示要显示刻度线
show_tick_labels    = yes  #选择yes表示要显示刻度线的数值
#定义刻度线的整体位置与形状
<ticks>                    #刻度线的转用标签，但凡是复数出现的，其下面的参数都表示全局参数，像下面的<tick>单数形式，都表示局部参数
radius           = 1r      #刻度线的位置，1r为最远距离，超过1r不再显示
color            = black
thickness        = 2p
multiplier       = 1e-6    #把刻度线标签（bp）缩小10万倍显示
format           = %d      #然后以整数的形式标记在刻度线上
#定义小的刻度线，且不显示数值
<tick>
spacing        = 5u        #最开始我们定义1u = 1000000，表示每500w bp显示一个小刻度线
size           = 10p
show_label     = no        #由于小的刻度线展示出来太密集，因此我们no不展示，默认不展示
</tick>
#定义大的刻度线，显示数值
<tick>
spacing        = 10u
size           = 15p
show_label     = yes
label_size     = 25p
label_offset   = 10p      #设置数值和刻度线之间的间隔
format         = %d
</tick>
</ticks>
#柱折线图-----------------------------------------------------------------------------------
<plots>
<plot>
type = line    #首先指明画图类型(histogram柱) 
file = ./data/SNPdensity.txt
r1   = 0.95r    #限制直方图显示的区域
r0   = 0.85r    #限制直方图显示的区域
fill_color = 26,129,228 #给直方图填充颜色
#stroke_color = white  #给直方图边框颜色
#stroke_thickness = 1p  #给直方图边框粗细
extend_bin = no  #一般情况下，柱形图也会有完整的一圈，但设置了extend_bin = no则不会显示没有value的区域
<rules>
</rules>
</plot>

<plot>
type = line    #首先指明画图类型(histogram柱) 
file = ./data/SNPdensity_gongyou.txt
r1   = 0.75r    #限制直方图显示的区域
r0   = 0.65r    #限制直方图显示的区域
fill_color = 26,228,125 #给直方图填充颜色
extend_bin = no  #一般情况下，柱形图也会有完整的一圈，但设置了extend_bin = no则不会显示没有value的区域
<rules>
</rules>
</plot>



<plot>
type = line    #首先指明画图类型(histogram柱) 
file = ./data/SNPdensity_Q1235teyou.txt
r1   = 0.60r    #限制直方图显示的区域
r0   = 0.50r    #限制直方图显示的区域
fill_color = 243,156,18  #给直方图填充颜色
extend_bin = no  #一般情况下，柱形图也会有完整的一圈，但设置了extend_bin = no则不会显示没有value的区域
<rules>
</rules>
</plot>


<plot>
type = line    #首先指明画图类型(histogram柱) 
file = ./data/SNPdensity_Q1teyou.txt
r1   = 0.40r    #限制直方图显示的区域
r0   = 0.30r    #限制直方图显示的区域
fill_color = 228,26,28 #给直方图填充颜色
extend_bin = no  #一般情况下，柱形图也会有完整的一圈，但设置了extend_bin = no则不会显示没有value的区域
<rules>
</rules>
</plot>

</plots>
#-----------------------------------------------------------------------------------
#下面是每次都要复制粘贴上去的，他们属于circos自带的配置文件，用于调用颜色，距离，报错等信息
<image>                    #注意路径
<<include software/circos/circos-0.69-9/etc/image.conf>> #注意引用外部配置文件需要使用<<#>>
</image>
<<include software/circos/circos-0.69-9/etc/colors_fonts_patterns.conf>> 
#官方没有提到下面的文件，但是没有这个文件会报错，所以还是加上去
<<include software/circos/circos-0.69-9/etc/housekeeping.conf>>

```

# 运行软件作图

```bash
touch conf/circos.conf
circos -conf conf/circos.conf
```
