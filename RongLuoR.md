# Running regressions and formatting with stargazer in R

# The setup:


```R
library(tidyverse)  #package needed to run the commands after installation of tidyverse 
```

    Warning message:
    "package 'tidyverse' was built under R version 3.6.3"


    Error: package or namespace load failed for 'tidyverse' in loadNamespace(j <- i[[1L]], c(lib.loc, .libPaths()), versionCheck = vI[[j]]):
     there is no package called 'reprex'
    Traceback:
    

    1. library(tidyverse)

    2. tryCatch({
     .     attr(package, "LibPath") <- which.lib.loc
     .     ns <- loadNamespace(package, lib.loc)
     .     env <- attachNamespace(ns, pos = pos, deps, exclude, include.only)
     . }, error = function(e) {
     .     P <- if (!is.null(cc <- conditionCall(e))) 
     .         paste(" in", deparse(cc)[1L])
     .     else ""
     .     msg <- gettextf("package or namespace load failed for %s%s:\n %s", 
     .         sQuote(package), P, conditionMessage(e))
     .     if (logical.return) 
     .         message(paste("Error:", msg), domain = NA)
     .     else stop(msg, call. = FALSE, domain = NA)
     . })

    3. tryCatchList(expr, classes, parentenv, handlers)

    4. tryCatchOne(expr, names, parentenv, handlers[[1L]])

    5. value[[3L]](cond)

    6. stop(msg, call. = FALSE, domain = NA)



```R
setwd('C:/Users/owenl/Documents/GitHub/RL/R')                                                #set the appropriate directory for R
read.csv('/Users/owenl/Documents/GitHub/RL/rankednewvar.csv')       #read the csv file that I made using python
```


<table>
<thead><tr><th scope=col>X</th><th scope=col>Unnamed..0</th><th scope=col>player</th><th scope=col>rank</th><th scope=col>wins</th><th scope=col>rating</th><th scope=col>link</th><th scope=col>Urls</th><th scope=col>Damaged.rounds</th><th scope=col>killToDeath.ratio</th><th scope=col>headshot..</th><th scope=col>win..</th><th scope=col>rating.wins</th></tr></thead>
<tbody>
	<tr><td> 0                                                                 </td><td> 0                                                                 </td><td>TSM FTX WARDELL                                                    </td><td> 1                                                                 </td><td>128                                                                </td><td>1257                                                               </td><td>/valorant/profile/riot/TSM%20FTX%20WARDELL%238266                  </td><td>https://tracker.gg/valorant/profile/riot/TSM%20FTX%20WARDELL%238266</td><td>171.2                                                              </td><td>1.44                                                               </td><td>0.23                                                               </td><td>0.57                                                               </td><td> 9.820312                                                          </td></tr>
	<tr><td> 1                                                                 </td><td> 1                                                                 </td><td>SoaR Cryo                                                          </td><td> 2                                                                 </td><td>139                                                                </td><td>1242                                                               </td><td>/valorant/profile/riot/SoaR%20Cryo%23Sadge                         </td><td>https://tracker.gg/valorant/profile/riot/SoaR%20Cryo%23Sadge       </td><td>172.4                                                              </td><td>1.32                                                               </td><td>0.29                                                               </td><td>0.66                                                               </td><td> 8.935252                                                          </td></tr>
	<tr><td> 2                                                                 </td><td> 2                                                                 </td><td>TSM FTX bang                                                       </td><td> 3                                                                 </td><td>150                                                                </td><td>1183                                                               </td><td>/valorant/profile/riot/TSM%20FTX%20bang%230000                     </td><td>https://tracker.gg/valorant/profile/riot/TSM%20FTX%20bang%230000   </td><td>156.5                                                              </td><td>1.17                                                               </td><td>0.39                                                               </td><td>0.54                                                               </td><td> 7.886667                                                          </td></tr>
	<tr><td> 3                                                                 </td><td> 3                                                                 </td><td>SoaR zander                                                        </td><td> 4                                                                 </td><td>133                                                                </td><td>1124                                                               </td><td>/valorant/profile/riot/SoaR%20zander%23swagy                       </td><td>https://tracker.gg/valorant/profile/riot/SoaR%20zander%23swagy     </td><td>151.1                                                              </td><td>1.24                                                               </td><td>0.28                                                               </td><td>0.67                                                               </td><td> 8.451128                                                          </td></tr>
	<tr><td> 4                                                                 </td><td> 4                                                                 </td><td>100T Asuna                                                         </td><td> 5                                                                 </td><td>111                                                                </td><td>1120                                                               </td><td>/valorant/profile/riot/100T%20Asuna%231111                         </td><td>https://tracker.gg/valorant/profile/riot/100T%20Asuna%231111       </td><td>167.3                                                              </td><td>1.19                                                               </td><td>0.26                                                               </td><td>0.58                                                               </td><td>10.090090                                                          </td></tr>
	<tr><td> 5                                                                 </td><td> 5                                                                 </td><td>COL brawk                                                          </td><td> 6                                                                 </td><td> 88                                                                </td><td>1109                                                               </td><td>/valorant/profile/riot/COL%20brawk%23420                           </td><td>https://tracker.gg/valorant/profile/riot/COL%20brawk%23420         </td><td>148.3                                                              </td><td>1.01                                                               </td><td>0.27                                                               </td><td>0.50                                                               </td><td>12.602273                                                          </td></tr>
	<tr><td> 6                                                                 </td><td> 6                                                                 </td><td>GUARD JonahP                                                       </td><td> 7                                                                 </td><td>143                                                                </td><td>1098                                                               </td><td>/valorant/profile/riot/GUARD%20JonahP%23ttv                        </td><td>https://tracker.gg/valorant/profile/riot/GUARD%20JonahP%23ttv      </td><td>160.1                                                              </td><td>1.18                                                               </td><td>0.29                                                               </td><td>0.63                                                               </td><td> 7.678322                                                          </td></tr>
	<tr><td> 7                                                                 </td><td> 7                                                                 </td><td>T1 JAWGEMO                                                         </td><td> 8                                                                 </td><td>133                                                                </td><td>1050                                                               </td><td>/valorant/profile/riot/T1%20JAWGEMO%23MOR                          </td><td>https://tracker.gg/valorant/profile/riot/T1%20JAWGEMO%23MOR        </td><td>161.2                                                              </td><td>1.13                                                               </td><td>0.24                                                               </td><td>0.54                                                               </td><td> 7.894737                                                          </td></tr>
	<tr><td> 8                                                                 </td><td> 8                                                                 </td><td>T1 curry                                                           </td><td> 9                                                                 </td><td>142                                                                </td><td>1040                                                               </td><td>/valorant/profile/riot/T1%20curry%230000                           </td><td>https://tracker.gg/valorant/profile/riot/T1%20curry%230000         </td><td>158.6                                                              </td><td>1.20                                                               </td><td>0.28                                                               </td><td>0.56                                                               </td><td> 7.323944                                                          </td></tr>
	<tr><td> 9                                                                 </td><td> 9                                                                 </td><td>SEN sinatraa                                                       </td><td>10                                                                 </td><td>104                                                                </td><td>1025                                                               </td><td>/valorant/profile/riot/SEN%20sinatraa%23idc                        </td><td>https://tracker.gg/valorant/profile/riot/SEN%20sinatraa%23idc      </td><td>163.8                                                              </td><td>1.13                                                               </td><td>0.18                                                               </td><td>0.56                                                               </td><td> 9.855769                                                          </td></tr>
	<tr><td>10                                                                 </td><td>10                                                                 </td><td>NRG hazed                                                          </td><td>11                                                                 </td><td>132                                                                </td><td>1015                                                               </td><td>/valorant/profile/riot/NRG%20hazed%23NRG                           </td><td>https://tracker.gg/valorant/profile/riot/NRG%20hazed%23NRG         </td><td>139.4                                                              </td><td>1.07                                                               </td><td>0.30                                                               </td><td>0.56                                                               </td><td> 7.689394                                                          </td></tr>
	<tr><td>11                                                                 </td><td>11                                                                 </td><td>SEN SicK                                                           </td><td>12                                                                 </td><td>120                                                                </td><td>1011                                                               </td><td>/valorant/profile/riot/SEN%20SicK%23xDDDD                          </td><td>https://tracker.gg/valorant/profile/riot/SEN%20SicK%23xDDDD        </td><td>180.9                                                              </td><td>1.34                                                               </td><td>0.27                                                               </td><td>0.60                                                               </td><td> 8.425000                                                          </td></tr>
	<tr><td>12                                                                 </td><td>12                                                                 </td><td>Lear                                                               </td><td>13                                                                 </td><td> 97                                                                </td><td>1010                                                               </td><td>/valorant/profile/riot/Lear%23anna                                 </td><td>https://tracker.gg/valorant/profile/riot/Lear%23anna               </td><td>147.7                                                              </td><td>1.12                                                               </td><td>0.27                                                               </td><td>0.56                                                               </td><td>10.412371                                                          </td></tr>
	<tr><td>13                                                                 </td><td>13                                                                 </td><td>NRG s0m                                                            </td><td>14                                                                 </td><td>202                                                                </td><td>1002                                                               </td><td>/valorant/profile/riot/NRG%20s0m%23NRG                             </td><td>https://tracker.gg/valorant/profile/riot/NRG%20s0m%23NRG           </td><td>160.4                                                              </td><td>1.21                                                               </td><td>0.31                                                               </td><td>0.54                                                               </td><td> 4.960396                                                          </td></tr>
	<tr><td>14                                                                 </td><td>14                                                                 </td><td>zt0L                                                               </td><td>15                                                                 </td><td>120                                                                </td><td> 997                                                               </td><td>/valorant/profile/riot/zt0L%23TTV                                  </td><td>https://tracker.gg/valorant/profile/riot/zt0L%23TTV                </td><td>145.0                                                              </td><td>1.13                                                               </td><td>0.20                                                               </td><td>0.54                                                               </td><td> 8.308333                                                          </td></tr>
	<tr><td>15                                                                 </td><td>15                                                                 </td><td>NRG tex                                                            </td><td>16                                                                 </td><td>112                                                                </td><td> 994                                                               </td><td>/valorant/profile/riot/NRG%20tex%23x1mob                           </td><td>https://tracker.gg/valorant/profile/riot/NRG%20tex%23x1mob         </td><td>162.7                                                              </td><td>1.20                                                               </td><td>0.34                                                               </td><td>0.60                                                               </td><td> 8.875000                                                          </td></tr>
	<tr><td>16                                                                 </td><td>16                                                                 </td><td>GUARD valyn                                                        </td><td>17                                                                 </td><td>107                                                                </td><td> 987                                                               </td><td>/valorant/profile/riot/GUARD%20valyn%23000                         </td><td>https://tracker.gg/valorant/profile/riot/GUARD%20valyn%23000       </td><td>  0.0                                                              </td><td>1.51                                                               </td><td>0.00                                                               </td><td>0.30                                                               </td><td> 9.224299                                                          </td></tr>
	<tr><td>17                                                                 </td><td>17                                                                 </td><td>V1 Zellsis                                                         </td><td>18                                                                 </td><td> 71                                                                </td><td> 975                                                               </td><td>/valorant/profile/riot/V1%20Zellsis%231240                         </td><td>https://tracker.gg/valorant/profile/riot/V1%20Zellsis%231240       </td><td>151.6                                                              </td><td>1.14                                                               </td><td>0.23                                                               </td><td>0.58                                                               </td><td>13.732394                                                          </td></tr>
	<tr><td>18                                                                 </td><td>18                                                                 </td><td>JASONR24                                                           </td><td>19                                                                 </td><td>249                                                                </td><td> 971                                                               </td><td>/valorant/profile/riot/JASONR24%239498                             </td><td>https://tracker.gg/valorant/profile/riot/JASONR24%239498           </td><td>142.2                                                              </td><td>1.03                                                               </td><td>0.24                                                               </td><td>0.51                                                               </td><td> 3.899598                                                          </td></tr>
	<tr><td>19                                                                 </td><td>19                                                                 </td><td>bearkun                                                            </td><td>20                                                                 </td><td>176                                                                </td><td> 956                                                               </td><td>/valorant/profile/riot/bearkun%23lily                              </td><td>https://tracker.gg/valorant/profile/riot/bearkun%23lily            </td><td>137.1                                                              </td><td>1.00                                                               </td><td>0.20                                                               </td><td>0.45                                                               </td><td> 5.431818                                                          </td></tr>
	<tr><td>20                                                                 </td><td>20                                                                 </td><td>two tone                                                           </td><td>21                                                                 </td><td> 79                                                                </td><td> 951                                                               </td><td>/valorant/profile/riot/two%20tone%2342p                            </td><td>https://tracker.gg/valorant/profile/riot/two%20tone%2342p          </td><td>154.3                                                              </td><td>1.13                                                               </td><td>0.25                                                               </td><td>0.53                                                               </td><td>12.037975                                                          </td></tr>
	<tr><td>21                                                                 </td><td>21                                                                 </td><td>XSET BcJ                                                           </td><td>22                                                                 </td><td>121                                                                </td><td> 951                                                               </td><td>/valorant/profile/riot/XSET%20BcJ%235FT2                           </td><td>https://tracker.gg/valorant/profile/riot/XSET%20BcJ%235FT2         </td><td>154.5                                                              </td><td>1.26                                                               </td><td>0.21                                                               </td><td>0.52                                                               </td><td> 7.859504                                                          </td></tr>
	<tr><td>22                                                                 </td><td>22                                                                 </td><td>NRG ANDROID                                                        </td><td>23                                                                 </td><td>115                                                                </td><td> 943                                                               </td><td>/valorant/profile/riot/NRG%20ANDROID%23NRG                         </td><td>https://tracker.gg/valorant/profile/riot/NRG%20ANDROID%23NRG       </td><td>157.7                                                              </td><td>1.31                                                               </td><td>0.34                                                               </td><td>0.59                                                               </td><td> 8.200000                                                          </td></tr>
	<tr><td>23                                                                 </td><td>23                                                                 </td><td>nillyaz                                                            </td><td>24                                                                 </td><td> 85                                                                </td><td> 926                                                               </td><td>/valorant/profile/riot/nillyaz%23sosa                              </td><td>https://tracker.gg/valorant/profile/riot/nillyaz%23sosa            </td><td>131.9                                                              </td><td>0.97                                                               </td><td>0.27                                                               </td><td>0.49                                                               </td><td>10.894118                                                          </td></tr>
	<tr><td>24                                                                 </td><td>24                                                                 </td><td>GHOST koalanoob                                                    </td><td>25                                                                 </td><td>164                                                                </td><td> 925                                                               </td><td>/valorant/profile/riot/GHOST%20koalanoob%23audry                   </td><td>https://tracker.gg/valorant/profile/riot/GHOST%20koalanoob%23audry </td><td>167.7                                                              </td><td>1.24                                                               </td><td>0.27                                                               </td><td>0.50                                                               </td><td> 5.640244                                                          </td></tr>
	<tr><td>25                                                                 </td><td>25                                                                 </td><td>OXG Reduxx                                                         </td><td>26                                                                 </td><td>103                                                                </td><td> 922                                                               </td><td>/valorant/profile/riot/OXG%20Reduxx%23sara                         </td><td>https://tracker.gg/valorant/profile/riot/OXG%20Reduxx%23sara       </td><td>172.7                                                              </td><td>1.21                                                               </td><td>0.28                                                               </td><td>0.55                                                               </td><td> 8.951456                                                          </td></tr>
	<tr><td>26                                                                 </td><td>26                                                                 </td><td>Habib                                                              </td><td>27                                                                 </td><td> 97                                                                </td><td> 920                                                               </td><td>/valorant/profile/riot/Habib%231234                                </td><td>https://tracker.gg/valorant/profile/riot/Habib%231234              </td><td>151.0                                                              </td><td>1.10                                                               </td><td>0.31                                                               </td><td>0.56                                                               </td><td> 9.484536                                                          </td></tr>
	<tr><td>27                                                                 </td><td>27                                                                 </td><td>geeza                                                              </td><td>28                                                                 </td><td>151                                                                </td><td> 919                                                               </td><td>/valorant/profile/riot/geeza%23POG                                 </td><td>https://tracker.gg/valorant/profile/riot/geeza%23POG               </td><td>154.7                                                              </td><td>1.12                                                               </td><td>0.36                                                               </td><td>0.45                                                               </td><td> 6.086093                                                          </td></tr>
	<tr><td>28                                                                 </td><td>28                                                                 </td><td>NV Victor                                                          </td><td>29                                                                 </td><td> 65                                                                </td><td> 911                                                               </td><td>/valorant/profile/riot/NV%20Victor%23777                           </td><td>https://tracker.gg/valorant/profile/riot/NV%20Victor%23777         </td><td>159.9                                                              </td><td>1.07                                                               </td><td>0.29                                                               </td><td>0.51                                                               </td><td>14.015385                                                          </td></tr>
	<tr><td>29                                                                 </td><td>29                                                                 </td><td>BM Light                                                           </td><td>30                                                                 </td><td>116                                                                </td><td> 907                                                               </td><td>/valorant/profile/riot/BM%20Light%23Sadge                          </td><td>https://tracker.gg/valorant/profile/riot/BM%20Light%23Sadge        </td><td>150.8                                                              </td><td>1.16                                                               </td><td>0.29                                                               </td><td>0.52                                                               </td><td> 7.818966                                                          </td></tr>
	<tr><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
	<tr><td>270                                                                </td><td>270                                                                </td><td>Bxdied                                                             </td><td>282                                                                </td><td> 91                                                                </td><td>626                                                                </td><td>/valorant/profile/riot/Bxdied%23Bod                                </td><td>https://tracker.gg/valorant/profile/riot/Bxdied%23Bod              </td><td>245.9                                                              </td><td>1.39                                                               </td><td>0.33                                                               </td><td>0.00                                                               </td><td> 6.879121                                                          </td></tr>
	<tr><td>271                                                                </td><td>271                                                                </td><td>EG BRANTED                                                         </td><td>283                                                                </td><td> 92                                                                </td><td>625                                                                </td><td>/valorant/profile/riot/EG%20BRANTED%23EVIL                         </td><td>https://tracker.gg/valorant/profile/riot/EG%20BRANTED%23EVIL       </td><td>127.1                                                              </td><td>0.92                                                               </td><td>0.31                                                               </td><td>0.51                                                               </td><td> 6.793478                                                          </td></tr>
	<tr><td>272                                                                </td><td>272                                                                </td><td>snxper                                                             </td><td>284                                                                </td><td>114                                                                </td><td>625                                                                </td><td>/valorant/profile/riot/snxper%23OTF                                </td><td>https://tracker.gg/valorant/profile/riot/snxper%23OTF              </td><td>150.5                                                              </td><td>1.09                                                               </td><td>0.34                                                               </td><td>0.53                                                               </td><td> 5.482456                                                          </td></tr>
	<tr><td>273                                                                </td><td>273                                                                </td><td>bouphapete                                                         </td><td>285                                                                </td><td> 90                                                                </td><td>625                                                                </td><td>/valorant/profile/riot/bouphapete%23NA1                            </td><td>https://tracker.gg/valorant/profile/riot/bouphapete%23NA1          </td><td>149.3                                                              </td><td>1.05                                                               </td><td>0.24                                                               </td><td>0.46                                                               </td><td> 6.944444                                                          </td></tr>
	<tr><td>274                                                                </td><td>274                                                                </td><td>BigBootyJudy                                                       </td><td>286                                                                </td><td> 80                                                                </td><td>625                                                                </td><td>/valorant/profile/riot/BigBootyJudy%23Here                         </td><td>https://tracker.gg/valorant/profile/riot/BigBootyJudy%23Here       </td><td>164.1                                                              </td><td>1.34                                                               </td><td>0.29                                                               </td><td>0.65                                                               </td><td> 7.812500                                                          </td></tr>
	<tr><td>275                                                                </td><td>275                                                                </td><td>AKREW Paincakes                                                    </td><td>287                                                                </td><td>157                                                                </td><td>624                                                                </td><td>/valorant/profile/riot/AKREW%20Paincakes%231738                    </td><td>https://tracker.gg/valorant/profile/riot/AKREW%20Paincakes%231738  </td><td>153.6                                                              </td><td>1.10                                                               </td><td>0.28                                                               </td><td>0.45                                                               </td><td> 3.974522                                                          </td></tr>
	<tr><td>276                                                                </td><td>276                                                                </td><td>Shadow                                                             </td><td>288                                                                </td><td>109                                                                </td><td>624                                                                </td><td>/valorant/profile/riot/Shadow%235152                               </td><td>https://tracker.gg/valorant/profile/riot/Shadow%235152             </td><td>126.1                                                              </td><td>0.96                                                               </td><td>0.27                                                               </td><td>0.50                                                               </td><td> 5.724771                                                          </td></tr>
	<tr><td>277                                                                </td><td>277                                                                </td><td>HighFlip                                                           </td><td>289                                                                </td><td> 91                                                                </td><td>624                                                                </td><td>/valorant/profile/riot/HighFlip%23Flip                             </td><td>https://tracker.gg/valorant/profile/riot/HighFlip%23Flip           </td><td>  0.0                                                              </td><td>1.81                                                               </td><td>0.00                                                               </td><td>0.57                                                               </td><td> 6.857143                                                          </td></tr>
	<tr><td>278                                                                </td><td>278                                                                </td><td>Jawmez                                                             </td><td>290                                                                </td><td>165                                                                </td><td>624                                                                </td><td>/valorant/profile/riot/Jawmez%231337                               </td><td>https://tracker.gg/valorant/profile/riot/Jawmez%231337             </td><td>153.9                                                              </td><td>1.16                                                               </td><td>0.32                                                               </td><td>0.43                                                               </td><td> 3.781818                                                          </td></tr>
	<tr><td>279                                                                </td><td>279                                                                </td><td>HIMACOPTER                                                         </td><td>291                                                                </td><td> 48                                                                </td><td>623                                                                </td><td>/valorant/profile/riot/HIMACOPTER%23ZZZ                            </td><td>https://tracker.gg/valorant/profile/riot/HIMACOPTER%23ZZZ          </td><td>134.7                                                              </td><td>0.93                                                               </td><td>0.21                                                               </td><td>0.44                                                               </td><td>12.979167                                                          </td></tr>
	<tr><td>280                                                                </td><td>280                                                                </td><td>Able chicago                                                       </td><td>293                                                                </td><td> 61                                                                </td><td>623                                                                </td><td>/valorant/profile/riot/Able%20chicago%23007                        </td><td>https://tracker.gg/valorant/profile/riot/Able%20chicago%23007      </td><td>156.0                                                              </td><td>1.12                                                               </td><td>0.29                                                               </td><td>0.46                                                               </td><td>10.213115                                                          </td></tr>
	<tr><td>281                                                                </td><td>281                                                                </td><td>HaanzeR                                                            </td><td>295                                                                </td><td>166                                                                </td><td>623                                                                </td><td>/valorant/profile/riot/HaanzeR%23manda                             </td><td>https://tracker.gg/valorant/profile/riot/HaanzeR%23manda           </td><td>140.1                                                              </td><td>0.96                                                               </td><td>0.25                                                               </td><td>0.49                                                               </td><td> 3.753012                                                          </td></tr>
	<tr><td>282                                                                </td><td>282                                                                </td><td>Nara                                                               </td><td>296                                                                </td><td> 68                                                                </td><td>623                                                                </td><td>/valorant/profile/riot/Nara%23604                                  </td><td>https://tracker.gg/valorant/profile/riot/Nara%23604                </td><td>133.0                                                              </td><td>1.00                                                               </td><td>0.27                                                               </td><td>0.52                                                               </td><td> 9.161765                                                          </td></tr>
	<tr><td>283                                                                </td><td>283                                                                </td><td>noli                                                               </td><td>297                                                                </td><td> 60                                                                </td><td>622                                                                </td><td>/valorant/profile/riot/noli%23liinz                                </td><td>https://tracker.gg/valorant/profile/riot/noli%23liinz              </td><td>154.5                                                              </td><td>1.17                                                               </td><td>0.30                                                               </td><td>0.53                                                               </td><td>10.366667                                                          </td></tr>
	<tr><td>284                                                                </td><td>284                                                                </td><td>BigPoppak                                                          </td><td>298                                                                </td><td>154                                                                </td><td>622                                                                </td><td>/valorant/profile/riot/BigPoppak%23dznut                           </td><td>https://tracker.gg/valorant/profile/riot/BigPoppak%23dznut         </td><td>151.3                                                              </td><td>1.03                                                               </td><td>0.29                                                               </td><td>0.49                                                               </td><td> 4.038961                                                          </td></tr>
	<tr><td>285                                                                </td><td>285                                                                </td><td>cutefatboy                                                         </td><td>299                                                                </td><td>171                                                                </td><td>621                                                                </td><td>/valorant/profile/riot/cutefatboy%23frick                          </td><td>https://tracker.gg/valorant/profile/riot/cutefatboy%23frick        </td><td>141.3                                                              </td><td>1.05                                                               </td><td>0.31                                                               </td><td>0.48                                                               </td><td> 3.631579                                                          </td></tr>
	<tr><td>286                                                                </td><td>286                                                                </td><td>ACE                                                                </td><td>300                                                                </td><td>117                                                                </td><td>620                                                                </td><td>/valorant/profile/riot/ACE%233131                                  </td><td>https://tracker.gg/valorant/profile/riot/ACE%233131                </td><td>144.3                                                              </td><td>1.01                                                               </td><td>0.25                                                               </td><td>0.49                                                               </td><td> 5.299145                                                          </td></tr>
	<tr><td>287                                                                </td><td>287                                                                </td><td>GenG gMd                                                           </td><td>301                                                                </td><td> 71                                                                </td><td>619                                                                </td><td>/valorant/profile/riot/GenG%20gMd%231337                           </td><td>https://tracker.gg/valorant/profile/riot/GenG%20gMd%231337         </td><td>153.3                                                              </td><td>1.08                                                               </td><td>0.29                                                               </td><td>0.49                                                               </td><td> 8.718310                                                          </td></tr>
	<tr><td>288                                                                </td><td>288                                                                </td><td>bork                                                               </td><td>302                                                                </td><td>138                                                                </td><td>619                                                                </td><td>/valorant/profile/riot/bork%23QwQ                                  </td><td>https://tracker.gg/valorant/profile/riot/bork%23QwQ                </td><td>142.1                                                              </td><td>1.10                                                               </td><td>0.25                                                               </td><td>0.52                                                               </td><td> 4.485507                                                          </td></tr>
	<tr><td>289                                                                </td><td>289                                                                </td><td>smallest borther                                                   </td><td>303                                                                </td><td> 73                                                                </td><td>618                                                                </td><td>/valorant/profile/riot/smallest%20borther%23burgr                  </td><td>https://tracker.gg/valorant/profile/riot/smallest%20borther%23burgr</td><td>154.9                                                              </td><td>1.11                                                               </td><td>0.24                                                               </td><td>0.56                                                               </td><td> 8.465753                                                          </td></tr>
	<tr><td>290                                                                </td><td>290                                                                </td><td>BERE                                                               </td><td>304                                                                </td><td> 70                                                                </td><td>617                                                                </td><td>/valorant/profile/riot/BERE%23tiff                                 </td><td>https://tracker.gg/valorant/profile/riot/BERE%23tiff               </td><td>139.9                                                              </td><td>1.01                                                               </td><td>0.34                                                               </td><td>0.42                                                               </td><td> 8.814286                                                          </td></tr>
	<tr><td>291                                                                </td><td>291                                                                </td><td>Nite                                                               </td><td>305                                                                </td><td> 48                                                                </td><td>617                                                                </td><td>/valorant/profile/riot/Nite%23King                                 </td><td>https://tracker.gg/valorant/profile/riot/Nite%23King               </td><td>143.7                                                              </td><td>1.05                                                               </td><td>0.20                                                               </td><td>0.46                                                               </td><td>12.854167                                                          </td></tr>
	<tr><td>292                                                                </td><td>292                                                                </td><td>k0rean                                                             </td><td>306                                                                </td><td>241                                                                </td><td>617                                                                </td><td>/valorant/profile/riot/k0rean%23JETT                               </td><td>https://tracker.gg/valorant/profile/riot/k0rean%23JETT             </td><td>144.7                                                              </td><td>1.03                                                               </td><td>0.16                                                               </td><td>0.47                                                               </td><td> 2.560166                                                          </td></tr>
	<tr><td>293                                                                </td><td>293                                                                </td><td>furbsa                                                             </td><td>307                                                                </td><td>161                                                                </td><td>617                                                                </td><td>/valorant/profile/riot/furbsa%23wnl                                </td><td>https://tracker.gg/valorant/profile/riot/furbsa%23wnl              </td><td>156.3                                                              </td><td>1.11                                                               </td><td>0.34                                                               </td><td>0.49                                                               </td><td> 3.832298                                                          </td></tr>
	<tr><td>294                                                                </td><td>294                                                                </td><td>bew                                                                </td><td>308                                                                </td><td>133                                                                </td><td>617                                                                </td><td>/valorant/profile/riot/bew%23666                                   </td><td>https://tracker.gg/valorant/profile/riot/bew%23666                 </td><td>156.8                                                              </td><td>1.26                                                               </td><td>0.30                                                               </td><td>0.70                                                               </td><td> 4.639098                                                          </td></tr>
	<tr><td>295                                                                </td><td>295                                                                </td><td>Apoth                                                              </td><td>309                                                                </td><td> 57                                                                </td><td>616                                                                </td><td>/valorant/profile/riot/Apoth%23DOG                                 </td><td>https://tracker.gg/valorant/profile/riot/Apoth%23DOG               </td><td>149.2                                                              </td><td>1.13                                                               </td><td>0.34                                                               </td><td>0.55                                                               </td><td>10.807018                                                          </td></tr>
	<tr><td>296                                                                </td><td>296                                                                </td><td>DZ ScrewFace                                                       </td><td>310                                                                </td><td> 91                                                                </td><td>616                                                                </td><td>/valorant/profile/riot/DZ%20ScrewFace%23MEOW                       </td><td>https://tracker.gg/valorant/profile/riot/DZ%20ScrewFace%23MEOW     </td><td>145.9                                                              </td><td>1.03                                                               </td><td>0.23                                                               </td><td>0.47                                                               </td><td> 6.769231                                                          </td></tr>
	<tr><td>297                                                                </td><td>297                                                                </td><td>Drink Water                                                        </td><td>311                                                                </td><td>163                                                                </td><td>616                                                                </td><td>/valorant/profile/riot/Drink%20Water%235252                        </td><td>https://tracker.gg/valorant/profile/riot/Drink%20Water%235252      </td><td>144.0                                                              </td><td>1.09                                                               </td><td>0.26                                                               </td><td>0.52                                                               </td><td> 3.779141                                                          </td></tr>
	<tr><td>298                                                                </td><td>298                                                                </td><td>WiLD                                                               </td><td>312                                                                </td><td> 86                                                                </td><td>615                                                                </td><td>/valorant/profile/riot/WiLD%23YAO                                  </td><td>https://tracker.gg/valorant/profile/riot/WiLD%23YAO                </td><td>136.6                                                              </td><td>1.00                                                               </td><td>0.26                                                               </td><td>0.44                                                               </td><td> 7.151163                                                          </td></tr>
	<tr><td>299                                                                </td><td>299                                                                </td><td>4fun player                                                        </td><td>313                                                                </td><td>145                                                                </td><td>615                                                                </td><td>/valorant/profile/riot/4fun%20player%23bear                        </td><td>https://tracker.gg/valorant/profile/riot/4fun%20player%23bear      </td><td>142.5                                                              </td><td>0.96                                                               </td><td>0.19                                                               </td><td>0.47                                                               </td><td> 4.241379                                                          </td></tr>
</tbody>
</table>




```R
ranks <- read.csv('/Users/owenl/Documents/GitHub/RL/rankednewvar.csv')      #name the dataframe, to ranks
```

## Experimental regressions I ran 

### First, I ran a regression model on ratings with respect to wins and rank. The regression showed that two of the intercept showed statistical significance with a good R-squared indicating a relatively good fit.  


```R
ols <- lm(rating ~ wins + rank , data = ranks)            #run a linear regression model with respect to the columns rating, wins and rank, where the data set is defined above as ranks. 
summary(ols)        #use the code to show the summary of the regression
```


    
    Call:
    lm(formula = rating ~ wins + rank, data = ranks)
    
    Residuals:
       Min     1Q Median     3Q    Max 
    -40.26 -28.78 -15.39  13.77 317.07 
    
    Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
    (Intercept) 933.75620    8.62772 108.227   <2e-16 ***
    wins          0.05771    0.05456   1.058    0.291    
    rank         -1.20907    0.03038 -39.795   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 47.17 on 297 degrees of freedom
    Multiple R-squared:  0.8461,	Adjusted R-squared:  0.8451 
    F-statistic: 816.5 on 2 and 297 DF,  p-value: < 2.2e-16
    


### Now to run a regression on wether Kill death ratio and average damage per round have any effect on rating. Assuming higher ranked players will have higher adr and Kill death ratio since they are in general, better. 


```R
ols2 <- lm(rating ~ Damaged.rounds + killToDeath.ratio, data = ranks)            #run a linear regression model with respect to the columns adr, kd and ratings, where the data set is defined above as ranks. 
summary(ols2)        #use the code to show the summary of the regression
```


    
    Call:
    lm(formula = rating ~ Damaged.rounds + killToDeath.ratio, data = ranks)
    
    Residuals:
        Min      1Q  Median      3Q     Max 
    -234.00  -91.77  -26.34   62.95  439.00 
    
    Coefficients:
                      Estimate Std. Error t value Pr(>|t|)    
    (Intercept)       516.2521    52.3338   9.865  < 2e-16 ***
    Damaged.rounds      0.6494     0.1711   3.795 0.000179 ***
    killToDeath.ratio 132.4138    33.0786   4.003 7.91e-05 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 116.2 on 297 degrees of freedom
    Multiple R-squared:  0.06562,	Adjusted R-squared:  0.05933 
    F-statistic: 10.43 on 2 and 297 DF,  p-value: 4.195e-05
    


## Robust regression: 


```R
library(estimatr)
```

    Warning message:
    "package 'estimatr' was built under R version 3.6.3"


```R
ols4_robust = lm_robust(rating.wins ~ rating + wins, data = ranks) #define the ols robust command same as above, with respect to ratingperwin and rating and wins, where data is the ranks dataframe
summary(ols4_robust)             #command to show the summary
```


    
    Call:
    lm_robust(formula = rating.wins ~ rating + wins, data = ranks)
    
    Standard error type:  HC2 
    
    Coefficients:
                 Estimate Std. Error t value  Pr(>|t|) CI Lower  CI Upper  DF
    (Intercept) 10.030220  0.6202450  16.171 1.254e-42  8.80959 11.250852 297
    rating       0.006064  0.0006725   9.017 2.451e-17  0.00474  0.007387 297
    wins        -0.058675  0.0035009 -16.760 7.746e-45 -0.06556 -0.051785 297
    
    Multiple R-squared:  0.7522 ,	Adjusted R-squared:  0.7505 
    F-statistic: 158.9 on 2 and 297 DF,  p-value: < 2.2e-16


# Regression Analysis:

## To answer my question of player efficiency in terms of gaining rank rating, I ran a regression model on the new variable I created, rating gained per win with respect to ranked rating and wins. The regression model showed all three variable having statistical significance. Moreover, for every increase in rating/win, the rating goes up by 0.006. The number is small yet indicates a slight positive correlation. The higher the player is on the leaderboard may correlated to being more efficient at winning. 


```R
ols4 <- lm(rating.wins ~ rating + wins, data = ranks) #define this as ols4, run the regression again like above except one column as the variable that was calculated rating/wins
summary(ols4)                     #show the summary of the regression 
```


    
    Call:
    lm(formula = rating.wins ~ rating + wins, data = ranks)
    
    Residuals:
        Min      1Q  Median      3Q     Max 
    -1.6937 -1.1144 -0.6152  0.4216  8.3443 
    
    Coefficients:
                  Estimate Std. Error t value Pr(>|t|)    
    (Intercept) 10.0302198  0.6330463  15.844  < 2e-16 ***
    rating       0.0060638  0.0008275   7.328 2.22e-12 ***
    wins        -0.0586750  0.0019614 -29.915  < 2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 1.693 on 297 degrees of freedom
    Multiple R-squared:  0.7522,	Adjusted R-squared:  0.7505 
    F-statistic: 450.8 on 2 and 297 DF,  p-value: < 2.2e-16
    


####  The above is my preferred regression model. Below is an explanation of why the above preferred regression model is better: 

#### Although both the Adjusted and multiple R-squared is better fitted on the 1st regressions, it does not achieve complete statistic significant. The 1st regression model are not significant in the wins category, indicated by the lack of signif codes under the wins coefficient. Hinting that there is little to no correlation between how much damage a player inflicts or how high his kill death ratio is in relations to his ranked rating, disproving my initial hypothesis that higher ranked players should have better stats. Lastly, my preferred regression achieves significant on both rating and wins. Moreover, the negative coefficient on wins, while holding rating constant, indicates a more holistic picture in terms of rank rating gained: as wins increase, ranked rating gained per win decreases, indicating there is a difficult time/a decrease in terms of efficiency in gaining rating. This is not apparent in the first three regressions.

# Running regression model to answer my second question of whether or not player's individual stats have an impact on wins and ranked ratings.

# First I see if the individual stats have an impact on total wins 

#### Regression of wins with respect to damage per round and kill death ratio 


```R
ols5 <- lm(wins ~ Damaged.rounds + killToDeath.ratio, data = ranks) #define this as ols5, run the regression again like above setting the variables as adr and kd
summary(ols5)                     #show the summary of the regression 
```


    
    Call:
    lm(formula = wins ~ Damaged.rounds + killToDeath.ratio, data = ranks)
    
    Residuals:
       Min     1Q Median     3Q    Max 
    -79.94 -34.23 -11.68  23.46 197.40 
    
    Coefficients:
                      Estimate Std. Error t value Pr(>|t|)    
    (Intercept)       94.71382   22.47652   4.214 3.33e-05 ***
    Damaged.rounds     0.18154    0.07349   2.470   0.0141 *  
    killToDeath.ratio -7.78439   14.20673  -0.548   0.5841    
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 49.91 on 297 degrees of freedom
    Multiple R-squared:  0.03166,	Adjusted R-squared:  0.02514 
    F-statistic: 4.855 on 2 and 297 DF,  p-value: 0.00842
    


#### Regression of wins with respect to headshot and kill death ratio 



```R
ols6 <- lm(wins ~ headshot.. + killToDeath.ratio, data = ranks) #define this as ols5, run the regression again like above setting the variables as hs and kd
summary(ols6)                     #show the summary of the regression 
```


    
    Call:
    lm(formula = wins ~ headshot.. + killToDeath.ratio, data = ranks)
    
    Residuals:
       Min     1Q Median     3Q    Max 
    -81.39 -32.90 -10.84  22.71 189.61 
    
    Coefficients:
                      Estimate Std. Error t value Pr(>|t|)    
    (Intercept)          93.00      21.26   4.375 1.69e-05 ***
    headshot..          100.72      35.60   2.829  0.00498 ** 
    killToDeath.ratio    -6.30      14.03  -0.449  0.65381    
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 49.76 on 297 degrees of freedom
    Multiple R-squared:  0.0377,	Adjusted R-squared:  0.03122 
    F-statistic: 5.818 on 2 and 297 DF,  p-value: 0.003323
    


# Next, I ran regression models to see if individual stats impact the amount of ratings gained per win

### These regressions were made to see if the more "efficient" players have higher individual stats vice versa:  

#### Regression of rating gained per win with respect to damage done per round and kill death ratio 


```R
ols7 <- lm(rating.wins ~ Damaged.rounds + killToDeath.ratio, data = ranks) #define this as ols7, run the regression again like above 
summary(ols7)                     #show the summary of the regression 
```


    
    Call:
    lm(formula = rating.wins ~ Damaged.rounds + killToDeath.ratio, 
        data = ranks)
    
    Residuals:
        Min      1Q  Median      3Q     Max 
    -6.7392 -2.4482 -0.3421  1.7077 12.5026 
    
    Coefficients:
                       Estimate Std. Error t value Pr(>|t|)    
    (Intercept)        6.709350   1.493194   4.493 1.01e-05 ***
    Damaged.rounds    -0.008156   0.004882  -1.671   0.0959 .  
    killToDeath.ratio  2.230021   0.943803   2.363   0.0188 *  
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 3.316 on 297 degrees of freedom
    Multiple R-squared:  0.04882,	Adjusted R-squared:  0.04242 
    F-statistic: 7.622 on 2 and 297 DF,  p-value: 0.0005913
    


#### Regression of rating gained per win with respect to Headshot and kill death ratio 


```R
ols8 <- lm(rating.wins ~ headshot.. + killToDeath.ratio, data = ranks) #define this as ols8, run the regression again like above 
summary(ols8)                     #show the summary of the regression 
```


    
    Call:
    lm(formula = rating.wins ~ headshot.. + killToDeath.ratio, data = ranks)
    
    Residuals:
        Min      1Q  Median      3Q     Max 
    -6.7770 -2.4536 -0.2267  1.6207 12.5872 
    
    Coefficients:
                      Estimate Std. Error t value Pr(>|t|)    
    (Intercept)          7.226      1.410   5.123 5.42e-07 ***
    headshot..          -5.524      2.362  -2.339   0.0200 *  
    killToDeath.ratio    1.988      0.931   2.135   0.0335 *  
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 3.301 on 297 degrees of freedom
    Multiple R-squared:  0.05725,	Adjusted R-squared:  0.0509 
    F-statistic: 9.018 on 2 and 297 DF,  p-value: 0.0001576
    


#### Regression of rating gained per win with respect to damage done per round and headshot


```R
ols9 <- lm(rating.wins ~ headshot.. + Damaged.rounds, data = ranks) #define this as ols9, run the regression again like above 
summary(ols9)                     #show the summary of the regression 
```


    
    Call:
    lm(formula = rating.wins ~ headshot.. + Damaged.rounds, data = ranks)
    
    Residuals:
        Min      1Q  Median      3Q     Max 
    -6.2128 -2.4381 -0.2479  1.6057 12.5666 
    
    Coefficients:
                     Estimate Std. Error t value Pr(>|t|)    
    (Intercept)     9.9898075  0.6168353  16.195   <2e-16 ***
    headshot..     -7.9058693  4.1248252  -1.917   0.0562 .  
    Damaged.rounds  0.0003325  0.0084114   0.040   0.9685    
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    Residual standard error: 3.326 on 297 degrees of freedom
    Multiple R-squared:  0.04278,	Adjusted R-squared:  0.03634 
    F-statistic: 6.637 on 2 and 297 DF,  p-value: 0.001514
    


# Using Stargazer to format the regression:

### Formating regression 5 and 6, total wins with respect to individual stats: 


```R
library(stargazer)         #function used to call upon the installed package 
stargazer(ols5, ols6, type = "html", column.labels = c("OLS5", "OLS6"), model.names = FALSE, #function to use stargazer with dataset from osl5 and ols6
          dep.var.caption = "Player stats",   #dependent variable caption 
          title            = "Regression of individual stats and total wins", #title of the regression table 
          covariate.labels = c("Damaged.rounds", "Killdeathratio", "Headshot"), #code for the codepedent variable label, here is all the individual stats
          dep.var.labels   = "wins", #code for the depedent variable label, here is the number of wins 
          out = "statsandwins.html") #code to output the html file saved under the path directed above with the function setwd
```

    
    <table style="text-align:center"><caption><strong>Regression of individual stats and total wins</strong></caption>
    <tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left"></td><td colspan="2">Player stats</td></tr>
    <tr><td></td><td colspan="2" style="border-bottom: 1px solid black"></td></tr>
    <tr><td style="text-align:left"></td><td colspan="2">wins</td></tr>
    <tr><td style="text-align:left"></td><td>OLS5</td><td>OLS6</td></tr>
    <tr><td style="text-align:left"></td><td>(1)</td><td>(2)</td></tr>
    <tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">Damaged.rounds</td><td>0.182<sup>**</sup></td><td></td></tr>
    <tr><td style="text-align:left"></td><td>(0.073)</td><td></td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td></tr>
    <tr><td style="text-align:left">Killdeathratio</td><td></td><td>100.719<sup>***</sup></td></tr>
    <tr><td style="text-align:left"></td><td></td><td>(35.597)</td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td></tr>
    <tr><td style="text-align:left">Headshot</td><td>-7.784</td><td>-6.300</td></tr>
    <tr><td style="text-align:left"></td><td>(14.207)</td><td>(14.033)</td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td></tr>
    <tr><td style="text-align:left">Constant</td><td>94.714<sup>***</sup></td><td>92.997<sup>***</sup></td></tr>
    <tr><td style="text-align:left"></td><td>(22.477)</td><td>(21.259)</td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td></tr>
    <tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">Observations</td><td>300</td><td>300</td></tr>
    <tr><td style="text-align:left">R<sup>2</sup></td><td>0.032</td><td>0.038</td></tr>
    <tr><td style="text-align:left">Adjusted R<sup>2</sup></td><td>0.025</td><td>0.031</td></tr>
    <tr><td style="text-align:left">Residual Std. Error (df = 297)</td><td>49.914</td><td>49.758</td></tr>
    <tr><td style="text-align:left">F Statistic (df = 2; 297)</td><td>4.855<sup>***</sup></td><td>5.818<sup>***</sup></td></tr>
    <tr><td colspan="3" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left"><em>Note:</em></td><td colspan="2" style="text-align:right"><sup>*</sup>p<0.1; <sup>**</sup>p<0.05; <sup>***</sup>p<0.01</td></tr>
    </table>
    

### Formating regression 7,8 and 9, ratings gained per win with respect to individual stats: 


```R
library(stargazer)         #function used to call upon the installed package 
stargazer(ols7, ols8, ols9, type = "html", column.labels = c("OLS7", "OLS8", "OLS9"), model.names = FALSE, #like above, except here it is three instead of two 
          dep.var.caption = "",   #the depedent variable caption
          title            = "Regression of individual stats and ratingsgainedperwin", #title for the regression table 
          covariate.labels = c("Damaged.rounds", "Killdeathratio", "Headshot"), #function for the labels of each of the variables 
          dep.var.labels   = "ratingsperwin", #code for the depedent variable label, here ratingsperwin
          out = "statsandratingwins.html") #code to output the html file saved under the path directed above with the function setwd
```

    
    <table style="text-align:center"><caption><strong>Regression of individual stats and ratingsgainedperwin</strong></caption>
    <tr><td colspan="4" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left"></td><td colspan="3">ratingsperwin</td></tr>
    <tr><td style="text-align:left"></td><td>OLS7</td><td>OLS8</td><td>OLS9</td></tr>
    <tr><td style="text-align:left"></td><td>(1)</td><td>(2)</td><td>(3)</td></tr>
    <tr><td colspan="4" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">Damaged.rounds</td><td>-0.008<sup>*</sup></td><td></td><td>0.0003</td></tr>
    <tr><td style="text-align:left"></td><td>(0.005)</td><td></td><td>(0.008)</td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td><td></td></tr>
    <tr><td style="text-align:left">Killdeathratio</td><td></td><td>-5.524<sup>**</sup></td><td>-7.906<sup>*</sup></td></tr>
    <tr><td style="text-align:left"></td><td></td><td>(2.362)</td><td>(4.125)</td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td><td></td></tr>
    <tr><td style="text-align:left">Headshot</td><td>2.230<sup>**</sup></td><td>1.988<sup>**</sup></td><td></td></tr>
    <tr><td style="text-align:left"></td><td>(0.944)</td><td>(0.931)</td><td></td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td><td></td></tr>
    <tr><td style="text-align:left">Constant</td><td>6.709<sup>***</sup></td><td>7.226<sup>***</sup></td><td>9.990<sup>***</sup></td></tr>
    <tr><td style="text-align:left"></td><td>(1.493)</td><td>(1.410)</td><td>(0.617)</td></tr>
    <tr><td style="text-align:left"></td><td></td><td></td><td></td></tr>
    <tr><td colspan="4" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">Observations</td><td>300</td><td>300</td><td>300</td></tr>
    <tr><td style="text-align:left">R<sup>2</sup></td><td>0.049</td><td>0.057</td><td>0.043</td></tr>
    <tr><td style="text-align:left">Adjusted R<sup>2</sup></td><td>0.042</td><td>0.051</td><td>0.036</td></tr>
    <tr><td style="text-align:left">Residual Std. Error (df = 297)</td><td>3.316</td><td>3.301</td><td>3.326</td></tr>
    <tr><td style="text-align:left">F Statistic (df = 2; 297)</td><td>7.622<sup>***</sup></td><td>9.018<sup>***</sup></td><td>6.637<sup>***</sup></td></tr>
    <tr><td colspan="4" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left"><em>Note:</em></td><td colspan="3" style="text-align:right"><sup>*</sup>p<0.1; <sup>**</sup>p<0.05; <sup>***</sup>p<0.01</td></tr>
    </table>
    
