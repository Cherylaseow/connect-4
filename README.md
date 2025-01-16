import random
import math


def check_move(board, turn, col, pop):
    turn1_con = board.count(1) != board.count(2)
    turn2_con = board.count(1) == board.count(2)
    if (turn1_con and turn == 1) or (turn2_con and turn == 2):
        return False
    if (col < 0) or (col > len(board) // 7):
        return False
    if pop:
        # check bottom to see if it's empty
        if board[col] == 0:
            return False
    # check top to see if it's filled
    elif board[len(board) - 7 + col] != 0:
        return False
    return True


def apply_move(board, turn, col, pop):
    if not check_move(board, turn, col, pop):
        return board.copy()
    if pop:
        for i in range(col, len(board) - 7, 7):
            board[i] = board[i + 7]
        board[len(board) - 7 + col] = 0
    else:
        for i in range(col, len(board) - 7, 7):
            if board[i] != 0:
                continue
            board[i] = turn
            break
    return board.copy()


def check_victory(board, who_played):
    # horizontals
    def condition_1(board):
        result_list = []
        # horizontal
        for i in range(0, len(board) // 7):
            for j in range(4):
                starting_index = 7 * i + j
                if len(set(board[starting_index:starting_index + 4])) == 1:
                    result_list.append(board[starting_index])
        return result_list if len(result_list) > 0 else 0

    # verticals
    def condition_2(board):
        result_list = []
        for i in range(0, len(board) - 21):
            check_list = []
            for j in range(4):
                check_list.append(board[i + 7 * j])
            if len(set(check_list)) == 1:
                result_list.append(check_list[0])
        return result_list if len(result_list) > 0 else 0

    # diagonals
    def condition_3(board):
        result_list = []
        for i in range(0, len(board) - 21):
            check_list_1 = []
            check_list_2 = []

            for j in range(4):
                # diagonal to the right
                if i % 7 < math.ceil(7 // 2):
                    check_list_1.append(board[i + 8 * j])
                else:
                    # diagonal to the left
                    check_list_2.append(board[i + 6 * j])
            if len(set(check_list_1)) == 1:
                result_list.append(check_list_1[0])
            elif len(set(check_list_2)) == 1:
                result_list.append(check_list_2[0])

        return result_list if len(result_list) > 0 else 0

    victory_con_1 = condition_1(board)
    victory_con_2 = condition_2(board)
    victory_con_3 = condition_3(board)

    victory = []
    for condition in [victory_con_1, victory_con_2, victory_con_3]:
        if type(condition) == int:
            victory.append(condition)
        else:
            victory.extend(condition)
    victory = set(victory)
    victory.remove(0)
    victory = list(victory)
    if len(victory) > 1:
        victory.remove(who_played)
    return victory[0] if len(victory) == 1 else 0


def computer_move(board, turn, level):
    non_pop_list = []
    pop_list = []
    turn_list = [1, 2]
    opponent_turn = turn_list.remove(turn)
    for column in range(7):
        non_pop = check_move(board, turn, column, False)
        if non_pop:
            non_pop_list.append(column)
        pop = check_move(board, turn, column, True)
        if pop:
            pop_list.append(column)
    if level == '1':
        choice_list = [non_pop_list, pop_list]
        pop = random.randint(0, 1)
        col = random.choice(choice_list[pop])
        return (col, bool(pop))
    else:
        victory_list = [[], []]
        deterrent_list = [[], []]
        board_copy_1 = board.copy()
        board_copy_2 = board.copy()
        for column in non_pop_list:
            apply_move(board_copy_1, turn, column, False)
            apply_move(board_copy_2, opponent_turn, column, False)

            if check_victory(board_copy_1, turn):
                victory_list[0].append(column)
            elif check_victory(board_copy_2, opponent_turn):
                deterrent_list[0].append(column)
            else:
                board_copy_1 = board.copy()
                board_copy_2 = board.copy()

        for column in pop_list:
            apply_move(board_copy_1, turn, column, True)
            apply_move(board_copy_2, opponent_turn, column, True)

            if check_victory(board, turn):
                victory_list[1].append(column)
            elif check_victory(board, opponent_turn):
                deterrent_list[1].append(column)
            else:
                board_copy_1 = board.copy()
                board_copy_2 = board.copy()

        if len(victory_list[0]) or len(victory_list[1]) != 0:
            pop = random.randint(0, 1)
            col = random.choice(victory_list[pop])
            return (col, bool(pop))
        elif len(deterrent_list[0]) or len(deterrent_list[1]) != 0:
            pop = random.randint(0, 1)
            col = random.choice(deterrent_list[pop])
            return (col, bool(pop))
        else:
            choice_list = [non_pop_list, pop_list]
            pop = random.randint(0, 1)
            col = random.choice(choice_list[pop])
            return (col, bool(pop))


def display_board(board):
    # for i in range(len(board), 0, -7):
    # print(str(board[i-7:i]))
    for i in range(0, len(board), 7):
        print(str(board[i:i + 7]))
    pass


def menu():
    # implement your function here
    pass


if __name__ == "__main__":
    menu()
