# tabyl2
## A tabyl() command with group_by() Functionality

The tabyl() function in the janitor library is a simple method for obtaining a frequency
table.
    
    mtcars %>% tabyl(mpg) # percentage (really proportion) of cars with 'x' mpg.

The problem, however, is that tabyl() doesn't seem to work with group_by():
    
    mtcars %>% group_by(cyl) %>% tabyl(mpg) # This yields the same results as the above example.

But, this problem can be solved by lifting the relevant count() and mutate() commands applied 
by the tabyl() function and by then using them directly (in the following order!):
    
    mtcars %>% group_by(cyl) %>% count(mpg) %>% mutate(percent = n / sum(n, na.rm = TRUE))

However, this method requires some extra syntax that we might want to avoid in the future. So, with
the end in mind of reducing how much typing we have to use in the future, I created the following
function: tabyl2()
    
    tabyl2 <- function (x, ..., wt = NULL, sort = FALSE) 
    {
      vars <- lazyeval::lazy_dots(...)
      wt <- substitute(wt)
      count_(x, vars, wt, sort = sort) %>%
        mutate(percent = n/sum(n, na.rm=TRUE))
    }

The guts are different, but the result is basically the same:

    # Compare
    mtcars %>% group_by(cyl) %>% tabyl2(mpg)
    # to
    mtcars %>% group_by(cyl) %>% count(mpg) %>% mutate(percent = n / sum(n, na.rm = TRUE))
