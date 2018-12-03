```k
module SPEC-IDS
    imports BUILTIN-ID-TOKENS
    syntax Name ::= #LowerId [token, autoReject]
                  | #UpperId [token, autoReject]
endmodule
```

``` {.k}
module TYPING-TESTS-SPEC
    imports PLUTUS-CORE-TYPING
    imports SPEC-IDS

    // builtin integer
    rule (con 1 ! 5)
      => [[ (con integer) (con 1) ]] @ (type)

    // lam, tyfun
    rule (lam x [[(con integer) (con 1)]] x)
      => (fun [[ (con integer) (con 1) ]] [[ (con integer) (con 1) ]]) @ (type)

    // builtin, tyall, tyfun
    rule (con addInteger)
      => (all s (size) (fun [[ (con integer) s ]] (fun [[ (con integer) s ]] [[ (con integer) s ]]))) @ (type)

    // app
    rule [ (lam x [[(con integer) (con 1)]] x) (con 1 ! 5) ]
      => [[ (con integer) (con 1) ]] @ (type)

    // abs
    rule (abs s (size) (lam x [[ (con integer) s ]] x)) => (all s (size) (fun [[ (con integer) s ]] [[ (con integer) s ]])) @ (type)

    // inst
    rule { (abs s (size) (lam x [[ (con integer) s ]] x)) (con 3) } => (fun [[ (con integer) (con 3) ]] [[ (con integer) (con 3) ]]) @ (type)

    // error
    rule (error (fun [[ (con integer) (con 1) ]] [[ (con integer) (con 1) ]])) => (fun [[ (con integer) (con 1) ]] [[ (con integer) (con 1) ]]) @ (type)

    // false
    // rule (abs a (type) (lam x a (lam y a y))) => (all a (type) (fun a (fun a a))) @ (type)

endmodule
```
