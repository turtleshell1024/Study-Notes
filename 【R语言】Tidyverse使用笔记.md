以下是deepseek生成的一篇关于 **tidyverse** 的使用笔记，内容涵盖核心包的基本操作和常用技巧，适合快速上手和日常参考。平时遇到什么新的用法了再补充上去。

---

# Tidyverse 使用笔记

## 1. 简介
**Tidyverse** 是 R 语言中一组专注于数据科学的高效、统一的包集合，核心思想是 **整洁数据（Tidy Data）**。主要包含以下包：
- `dplyr`: 数据操作
- `ggplot2`: 数据可视化
- `tidyr`: 数据清洗与整理
- `readr`: 读取结构化数据
- `purrr`: 函数式编程
- `forcats`: 处理因子变量
- `stringr`: 字符串处理
- `lubridate`: 日期时间处理

安装与加载：
```r
install.packages("tidyverse")  # 安装所有包
library(tidyverse)            # 加载核心包（dplyr, ggplot2, tidyr等）
```

---

## 2. 数据操作（dplyr）
### 核心函数
- `filter()`: 按条件筛选行  
  ```r
  starwars %>% filter(species == "Human", height > 160)
  ```

- `select()`: 选择列  
  ```r
  starwars %>% select(name, height, mass)
  ```

- `mutate()`: 创建新列或修改列  
  ```r
  starwars %>% mutate(bmi = mass / (height/100)^2)
  ```

- `arrange()`: 按列排序  
  ```r
  starwars %>% arrange(desc(mass))
  ```

- `group_by()` + `summarise()`: 分组汇总  
  ```r
  starwars %>%
    group_by(species) %>%
    summarise(mean_height = mean(height, na.rm = TRUE))
  ```

- `left_join()`: 合并表  
  ```r
  left_join(table1, table2, by = "id")
  ```
  
- `separate()`: 分割列  
  ```r
  df <- df %>%
  separate(col, into = c("left_part", "right_part"), sep = ":")

  ```
---

## 3. 数据整理（tidyr）
### 长宽格式转换
- `pivot_longer()`: 宽表转长表  
  ```r
  wide_data %>% pivot_longer(cols = -id, names_to = "year", values_to = "value")
  ```

- `pivot_wider()`: 长表转宽表  
  ```r
  long_data %>% pivot_wider(names_from = "year", values_from = "value")
  ```

### 处理缺失值
- `drop_na()`: 删除含缺失值的行  
  ```r
  data %>% drop_na()
  ```

- `fill()`: 填充缺失值（向前/向后填充）  
  ```r
  data %>% fill(value, .direction = "down")
  ```

---

## 4. 数据可视化（ggplot2）
### 基本语法
```r
ggplot(data, aes(x = var1, y = var2)) +  # 初始化画布与映射
  geom_point() +                        # 添加几何对象（散点图）
  labs(title = "Title", x = "X Label")  # 添加标签
```

### 常用几何对象
- `geom_line()`: 折线图  
- `geom_bar(stat = "identity")`: 柱状图  
- `geom_boxplot()`: 箱线图  
- `geom_histogram()`: 直方图  

### 高级调整
- 分面：`facet_wrap(~var)` 或 `facet_grid(row ~ col)`  
- 主题：`theme_bw()`, `theme_minimal()`  
- 颜色标尺：`scale_color_brewer(palette = "Set1")`  

示例：
```r
ggplot(mpg, aes(x = displ, y = hwy, color = class)) +
  geom_point() +
  facet_wrap(~year) +
  theme_light()
```

---

## 5. 函数式编程（purrr）
### 替代循环的 `map` 函数族
- `map(.x, .f)`: 返回列表  
- `map_dbl()`: 返回数值向量  
- `map_chr()`: 返回字符向量  

示例：批量读取 CSV 文件并合并  
```r
files <- list.files("data/", pattern = "*.csv", full.names = TRUE)
data_list <- map(files, read_csv)
combined_data <- bind_rows(data_list)
```

---

## 6. 字符串处理（stringr）
### 常用函数
- `str_detect()`: 检测字符串模式  
  ```r
  str_detect(names, "John")
  ```

- `str_replace()`: 替换字符串  
  ```r
  str_replace(text, "old", "new")
  ```

- `str_split()`: 拆分字符串  
  ```r
  str_split(dates, "-")
  ```

---

## 7. 实际案例
### 完整数据处理流程
```r
# 1. 读取数据
data <- read_csv("data.csv")

# 2. 清洗数据
clean_data <- data %>%
  filter(!is.na(value)) %>%
  mutate(date = as.Date(date)) %>%
  pivot_longer(cols = starts_with("var"), names_to = "variable")

# 3. 分析并可视化
clean_data %>%
  group_by(variable, year = year(date)) %>%
  summarise(mean_value = mean(value)) %>%
  ggplot(aes(x = year, y = mean_value, color = variable)) +
  geom_line() +
  labs(title = "趋势分析")
```

---

## 8. 小贴士
1. 使用 `%>%`（管道符）提高代码可读性。
2. 用 `na.rm = TRUE` 处理函数中的缺失值（如 `mean(x, na.rm = TRUE)`）。
3. 调试时逐步运行管道：选中部分代码按 `Ctrl + Enter`（RStudio）。

---

通过以上工具，Tidyverse 能显著提升数据处理的效率和代码的可维护性。建议结合官方文档（[tidyverse.org](https://www.tidyverse.org/)）和《R for Data Science》深入学习。
