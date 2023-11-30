# Async Basics

"Async" is a concurrency model where multiple tasks are executed concurrently by
executing each task until it must wait for some other event, then switching to
another task that is ready to make progress. The model allows running a larger
number of tasks on a limited number of threads. This is because the per-task
overhead is typically very low and operating systems provide primitives for
efficiently identifying I/O that is able to proceed.

{{%segment outline}}
