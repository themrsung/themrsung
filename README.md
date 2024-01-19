    int x = 1;
    int y = 2;
    int z = IntStream.of(x, y)
        .reduce(0, (a, b) -> a + b);
