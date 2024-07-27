CC = gcc
UNAME := $(shell uname)
ifeq ($(UNAME), Darwin)
    LDFLAGS = -lcheck -lpthread -lm -lgcov
else
    LDFLAGS = -lcheck -lpthread -lrt -lm -lsubunit -lgcov
endif

CFLAGS = -Wall -Werror -Wextra -Wdeprecated-declarations -Wpedantic -pedantic -std=c11 -fprofile-arcs -ftest-coverage
OBJ = arthmetic.o comparision.o convertors.o help_functions.o other.o
LIB = s21_decimal.a
TEST_OBJ = TESTS/SUITE_main.o

# HTML report directories-lcheck -lpthread -lm -lsubunit -lgcov
GCOV_REPORT_DIR = gcov_report

all: clean $(LIB) test

$(LIB): $(OBJ)
	ar rcs $(LIB) $(OBJ)

test: $(TEST_OBJ) $(LIB)
	$(CC) $(CFLAGS) -o test.out $(TEST_OBJ) $(LIB) $(LDFLAGS)
	make run_tests

run_tests:
	./test.out

# Generate gcov report
gcov_report: run_tests
	mkdir -p $(GCOV_REPORT_DIR)
	lcov --capture --directory . --output-file coverage.info
	lcov --remove coverage.info '*/tests/*' '*/usr/*' --output-file coverage.info --ignore-errors unused
	genhtml coverage.info --output-directory $(GCOV_REPORT_DIR)

open_report: gcov_report
ifeq ($(UNAME), Darwin)
	# For macOS, use open command
	open $(GCOV_REPORT_DIR)/index.html
else
	# For Linux, use xdg-open
	xdg-open $(GCOV_REPORT_DIR)/index.html
endif

arthmetic.o: arthmetic.c s21_decimal.h
	$(CC) $(CFLAGS) -c arthmetic.c -o arthmetic.o

comparision.o: comparision.c s21_decimal.h
	$(CC) $(CFLAGS) -c comparision.c -o comparision.o

convertors.o: convertors.c s21_decimal.h
	$(CC) $(CFLAGS) -c convertors.c -o convertors.o

help_functions.o: help_functions.c s21_help.h
	$(CC) $(CFLAGS) -c help_functions.c -o help_functions.o

other.o: other.c s21_help.h
	$(CC) $(CFLAGS) -c other.c -o other.o

TESTS/SUITE_main.o: TESTS/SUITE_main.c s21_decimal.h
	$(CC) $(CFLAGS) -c TESTS/SUITE_main.c -o TESTS/SUITE_main.o

check:
	clang-format -style=Google -n ./*.c ./*.h

rebuild: clean all

clean:
	rm -f $(OBJ) $(LIB) test.out TESTS/SUITE_main.o
	rm -f coverage.info
	rm -rf $(GCOV_REPORT_DIR)
	rm -f *.gcda *.gcno TESTS/*.gcda TESTS/*.gcno

.PHONY: all clean test run_tests gcov_report open_report
