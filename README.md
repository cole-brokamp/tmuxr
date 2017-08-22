
<!-- README.md is generated from README.Rmd. Please edit that file -->
<img width="100px" src="https://datascienceworkshops.github.io/tmuxr/reference/figures/tmuxr.png" style="float: right; margin-top: 50px;" />

tmuxr
=====

The `tmuxr` package allows you to control [tmux](https://github.com/tmux/tmux/wiki) from R. Using a pipeable API, you can create, control, and record tmux sessions, windows, and panes.

Most functions, such as `new_session`, `list_windows`, and `send_keys` are inspired by the commands `tmux` offers. Other functions, such as `session_from_name`, `wait_for_prompt`, `send_lines` are added for convenience. Please note that not all `tmux` commands have yet been implemented.

### Installation

You can install `tmuxr` from GitHub with:

``` r
# install.packages("devtools")
devtools::install_github("datascienceworkshops/tmuxr")
```

### Examples

``` r
library(tmuxr)
```

#### Bash

``` r
s <- new_session(shell_command = "PS1='$ ' bash",
                 prompt = prompts$bash)
wait_for_prompt(s)
send_lines(s, c("seq 100 |",
               "grep 3 |",
               "wc -l ",
               "date"))
capture_pane(s, trim = TRUE)
```

    ## [1] "$ seq 100 |"                   "> grep 3 |"                   
    ## [3] "> wc -l"                       "      19"                     
    ## [5] "$ date"                        "Tue Aug 22 21:43:20 CEST 2017"

``` r
kill_session(s)
```

<!-- #### Full screen capture -->
<!-- ```{r} -->
<!-- new_session() %>% -->
<!--   send_keys("htop") %>% -->
<!--   send_enter() %>% -->
<!--   wait(2) %>% -->
<!--   capture_pane(as_message = TRUE) %>% -->
<!--   send_keys("q") -->
<!-- ``` -->
#### Jupyter console

``` r
jupyter <- new_session(name = "python",
                       shell_command = "jupyter console",
                       prompt = prompts$jupyter)

jupyter$prompt
```

    ## [1] "^(In \\[[0-9]+\\]| {6,})|$"

``` r
jupyter %>%
  wait_for_prompt() %>%
  send_lines(c("def mysum(a, b):",
               "return a + b",
               "",
               "")) %>%
  capture_pane(as_message = TRUE, strip_lonely_prompt = FALSE, trim = TRUE)
```

    ## Jupyter console 5.1.0
    ## 
    ## Python 3.6.1 |Anaconda 4.4.0 (x86_64)| (default, May 11 2017, 13:04:09)
    ## Type "copyright", "credits" or "license" for more information.
    ## 
    ## IPython 5.3.0 -- An enhanced Interactive Python.
    ## ?         -> Introduction and overview of IPython's features.
    ## %quickref -> Quick reference.
    ## help      -> Python's own help system.
    ## object?   -> Details about 'object', use 'object??' for extra details.
    ## 
    ## 
    ## 
    ## In [1]: def mysum(a, b):
    ##       :     return a + b
    ##       :
    ##       :

<!-- #### Telnet -->
<!-- ```{r, cache=TRUE} -->
<!-- new_session(shell_command = "telnet", prompt = "^telnet>$") %>% -->
<!--   send_keys("open towel.blinkenlights.nl") %>% -->
<!--   send_enter() %>% -->
<!--   wait(26) %>% -->
<!--   capture_pane(as_message = TRUE) %>% -->
<!--   kill_session() -->
<!-- ``` -->
#### Continue with earlier session

``` r
session_from_name("python", prompt = prompts$jupyter) %>%
  wait(0.2) %>%
  send_lines("mysum(41, 1)") %>%
  wait(0.2) %>%
  capture_pane(start = 18, as_message = TRUE)
```

    ## In [2]: mysum(41, 1)
    ## Out[2]: 42
    ## 
    ## In [3]:

``` r
list_sessions()
```

    ## [[1]]
    ## tmuxr session 0: 1 windows (created Tue Aug 22 21:32:02 2017) [80x23]
    ## [[2]]
    ## tmuxr session python: 1 windows (created Tue Aug 22 21:43:20 2017) [80x23]

``` r
kill_server()
```

    ## character(0)

### License

The `tmuxr` package is licensed under the GPLv3 (<http://www.gnu.org/licenses/gpl.html>).
