# Обновление
# Не работает 4 часть. Почему?

from aiogram import Bot, executor, Dispatcher, types
from aiogram.types import ReplyKeyboardMarkup, KeyboardButton
from aiogram.contrib.fsm_storage.memory import MemoryStorage
from aiogram.dispatcher.filters.state import StatesGroup, State
from aiogram.dispatcher import FSMContext

token = "???"

storage = MemoryStorage()
bot = Bot(token="???")
dp = Dispatcher(bot, storage=storage)

kb_client = ReplyKeyboardMarkup()
b1 = KeyboardButton("1")
b2 = KeyboardButton("2")
b3 = KeyboardButton("3")
b4 = KeyboardButton("4")
b0 = KeyboardButton("0")

kb_client.row(b1, b2)
kb_client.row(b3, b4)
kb_client.add(b0)

class Math1(StatesGroup):
    N1 = State()

class Math2(StatesGroup):
    N1 = State()

class Math3(StatesGroup):
    N1 = State()
    N2 = State()
    N3 = State()
    N31 = State()
    N32 = State()
    N33 = State()
    N333 = State()

class Math4(StatesGroup):
    N1 = State()
    N2 = State()
    N3 = State()
    N4 = State()
    N41 = State()
    N42 = State()
    N43 = State()
    N44 = State()

@dp.message_handler(commands=["start"])
async def cmd_start(msg: types.Message) -> None:
    await msg.answer(
        f"Приветствуем, {msg.from_user.first_name}! Это решатор, который поможет вам с некоторыми математическими задачами",
        reply_markup=kb_client)
    await msg.answer("Мы можем помочь вам со следующими пунктами:")
    await msg.answer("1. Решение линейных уравнений")
    await msg.answer("2. Решение квадратных уравнений")
    await msg.answer("3. Построение арифметической прогрессии")
    await msg.answer("4. Арифметические операции с дробями")
    await msg.answer("Введите номер интересующего вас пункта")

# РЕШЕНИЕ ЛИНЕЙНОГО УРАВНЕНИЯ
@dp.message_handler(lambda msg: msg.text == "1")
async def choice_1(msg: types.Message) -> None:
    await msg.answer("Общий вид линейного уравнения: ax + b = 0")
    await msg.answer("Введите значения коэффициентов a и b через пробел")
    await Math1.N1.set()

@dp.message_handler(state=Math1.N1)
async def choice_1_N1(msg: types.Message, state: FSMContext) -> None:
    a, b = map(int, msg.text.split())
    if a != 0:
        x = -b / a
        await msg.answer(f"Ответ: x = {x}")
    else:
        await msg.answer("В линейном уравнении коэффициент a не может быть равен нулю.")
    await state.finish()

# РЕШЕНИЕ КВАДРАТНОГО УРАВНЕНИЯ
@dp.message_handler(lambda msg: msg.text == "2")
async def choice_2(msg: types.Message) -> None:
    await msg.answer("Общий вид квадратного уравнения: ax^2 + bx + c = 0")
    await msg.answer("Введите значения коэффициентов a, b и c через пробел")
    await Math2.N1.set()

@dp.message_handler(state=Math2.N1)
async def choice_2_N1(msg: types.Message, state: FSMContext) -> None:
    a, b, c = map(int, msg.text.split())
    if a != 0:
        D = b ** 2 - 4 * a * c
        if D > 0:
            x1 = (-b + D ** 0.5) / (2 * a)
            x2 = (-b - D ** 0.5) / (2 * a)
            await msg.answer(f"Ответ: x1 = {x1}, x2 = {x2}")
        elif D == 0:
            x = -b / (2 * a)
            await msg.answer(f"Ответ: x = {x}")
        else:
            await msg.answer(f"Данное уравнение не имеет решений")
    else:
        await msg.answer("В квадратном уравнении коэффициент a не может быть равен нулю.")
    await state.finish()

# РАБОТА С АРИФМЕТИЧЕСКОЙ ПРОГРЕССИЕЙ
@dp.message_handler(lambda msg: msg.text == "3")
async def choice_3(msg: types.Message) -> None:
    await msg.answer("Введите значение первого элемента прогрессии")
    await Math3.N1.set()

@dp.message_handler(state=Math3.N1)
async def choice_3_N1(msg: types.Message, state: FSMContext) -> None:
    start = int(msg.text)
    await state.update_data(N1=start)
    await msg.answer("Введите значение разности между соседними элементами")
    await Math3.N2.set()

@dp.message_handler(state=Math3.N2)
async def choice_3_N2(msg: types.Message, state: FSMContext) -> None:
    step = int(msg.text)
    await state.update_data(N2=step)
    await msg.answer('Выберите один из трех пунктов, введя соответствующий номер')
    await msg.answer("1. Вывести все элементы прогрессии")
    await msg.answer("2. Вывести номер элемента прогрессии, равного определенному значению")
    await msg.answer("3. Вывести значение элемента прогрессии под определенным номером")
    await Math3.N3.set()

@dp.message_handler(state=Math3.N3)
async def choice_3_N3(msg: types.Message, state: FSMContext) -> None:
    choice = msg.text
    if choice == "1":
        await Math3.N31.set()
        await msg.answer("Введите количество элементов в прогрессии")
    elif choice == "2":
        await Math3.N32.set()
        await msg.answer("Введите значение элемента прогрессии:")
    elif choice == "3":
        await Math3.N33.set()
        await msg.answer("Введите номер элемента прогрессии:")
    else:
        await msg.answer("Такой опции не существует.")
        await state.finish()

@dp.message_handler(state=Math3.N31)
async def choice_3_N31(msg: types.Message, state: FSMContext) -> None:
    elements = int(msg.text)
    await msg.answer('Получившая арифметическая прогрессия:')
    start = await state.get_data('N1')
    step = await state.get_data('N2')
    for i in range(start.get('N1'), start.get('N1') + (elements - 1) * step.get('N2') + 1, step.get('N2')):
        await msg.answer(i)
    await msg.answer('stop')
    await state.finish()

@dp.message_handler(state=Math3.N32)
async def choice_3_N32(msg: types.Message, state: FSMContext) -> None:
    value = int(msg.text)
    start = await state.get_data('N1')
    step = await state.get_data('N2')
    start1 = int(start.get('N1'))
    step1 = int(step.get('N2'))
    if (value - start.get('N1')) % step.get('N2') == 0:
        number = 1
        while start1 < value:
            start1 = start1 + step1
            number += 1
        await msg.answer(f"Элемент со значением {value} находится под номером {number}")
    else:
        await msg.answer(f"Значение {value} отсутствует в данной арифметической прогрессии.")
    await state.finish()

@dp.message_handler(state=Math3.N33)
async def choice_3_N33(msg: types.Message, state: FSMContext) -> None:
    number = int(msg.text)
    start = await state.get_data('N1')
    step = await state.get_data('N2')
    if number > 0:
        value = start.get('N1') + (step.get('N2') * (number - 1))
        await msg.answer(f"Элемент под номером {number} имеет значение {value}")
        await state.finish()
    else:
        await msg.answer("Введите положительный номер.")
        await Math3.N333.set()

@dp.message_handler(state=Math3.N333)
async def choice_3_N333(msg: types.Message, state: FSMContext) -> None:
    number = int(msg.text)
    start = await state.get_data('N1')
    step = await state.get_data('N2')
    if number > 0:
        value = start.get('N1') + (step.get('N2') * (number - 1))
        await msg.answer(f"Элемент под номером {number} имеет значение {value}")
        await state.finish()
    else:
        await msg.answer("Введите положительный номер.")
        await Math3.N33.set()

# ОПЕРАЦИИ С ДРОБЯМИ
@dp.message_handler(lambda msg: msg.text == "4")
async def choice_4(msg: types.Message) -> None:
    await msg.answer("Введите числитель и знаменатель первой дроби через пробел")
    await Math4.N1.set()

@dp.message_handler(state=Math4.N1)
async def choice_4_N1(msg: types.Message, state: FSMContext) -> None:
    x1, y1 = map(int, input().split())
    await msg.answer("Введите числитель и знаменатель второй дроби через пробел")
    await state.update_data(N1=x1)
    await state.update_data(N2=y1)
    await Math4.N2.set()

@dp.message_handler(state=Math4.N2)
async def choice_4_N2(msg: types.Message, state: FSMContext) -> None:
    x2, y2 = map(int, input().split())
    await state.update_data(N3=x2)
    await state.update_data(N4=y2)
    y1 = await state.get_data('N2')
    if y1.get('N2') == 0 or y2 == 0:
        await msg.answer("Знаменатель дроби не может равняться 0.")
        await state.finish()
    else:
        await msg.answer('Введите номер операции, которую вы хотите применить к данным дробям')
        await msg.answer("1. +")
        await msg.answer("2. -")
        await msg.answer("3. *")
        await msg.answer("4. :")
        await Math4.N3.set()

@dp.message_handler(state=Math4.N3)
async def choice_4_N3(msg: types.Message, state: FSMContext) -> None:
    choice = msg.text
    x1 = await state.get_data('N1')
    y1 = await state.get_data('N2')
    x2 = await state.get_data('N3')
    y2 = await state.get_data('N4')
    x1 = int(x1.get('N1'))
    y1 = int(y1.get('N2'))
    x2 = int(x2.get('N3'))
    y2 = int(y2.get('N4'))
    if choice == "1":
        x3 = x1 * y2 + x2 * y1
        y3 = y1 * y2
        await msg.answer(f"{x1}/{y1} + {x2}/{y2} =")
    elif choice == "2":
        x3 = x1 * y2 - x2 * y1
        y3 = y1 * y2
        await msg.answer(f"{x1}/{y1} - {x2}/{y2} =")
        flag = False
    elif choice == "3":
        x3 = x1 * x2
        y3 = y1 * y2
        await msg.answer(f"{x1}/{y1} * {x2}/{y2} =")
    elif choice == "4":
        if x2 == 0:
            await msg.answer("Числитель второй дроби не может равняться 0.")
        else:
            x3 = x1 * y2
            y3 = y1 * x2
            await msg.answer(f"{x1}/{y1} / {x2}/{y2} =")
    else:
        await msg.answer("Такой опции не существует.")
        await state.finish()
    if flag is True and x2 != 0 and choice in list("1234"):  # ["1", "2", "3", "4"]
        NOD = x3
        if y3 < x3:
            NOD = y3
        while not (x3 % NOD == 0 and y3 % NOD == 0):
            NOD -= 1
        x3 //= NOD
        y3 //= NOD
        await msg.answer(f"{x3}/{y3}")
    await state.finish()

# @dp.message_handler(lambda msg: msg.text == "0")
# async def choice_0(msg: types.Message) -> None:
#     await msg.answer("Такой опции не существует.")
#     await msg.answer("Благодарим вас за использование нашего сервиса! До встречи!")

@dp.message_handler(commands=["cancel"])
async def cmd_cancel(msg: types.Message) -> None:
    await msg.answer("Canceled", reply_markup=types.ReplyKeyboardRemove())

if __name__ == "__main__":
    executor.start_polling(dispatcher=dp)
