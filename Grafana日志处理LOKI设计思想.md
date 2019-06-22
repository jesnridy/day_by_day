# Grafana日志处理LOKI设计思想

- 日常工作中出现的问题是从告警开始，告警是通过一些度量值来进行触发，详细的问题还是需要查看日志去发现。所以Loki就是要先解决掉度量和日志之间的上下文切换成本，减少trouble shoot时间。


- **参考阅读:**<https://docs.google.com/document/d/11tjK_lvp1-SVsFZjgOTr1vV3-q6vBAsZYIQ5ZeYBkyM/view#>