import sqlite3
conn = sqlite3.connect('msg.db')
c = conn.cursor()
c.execute('''CREATE TABLE IF NOT EXISTS SONG
    (song    TEXT    NOT NULL,
    recommend   TEXT    NOT NULL);''')  # 依次为歌名、安利语
c.execute('''CREATE TABLE IF NOT EXISTS LISTENERS
    (openid    TEXT    NOT NULL,
    get_count    int,
    put_count    int);''')  # 依次为用户ID、捞歌次数、安利次数


def get_song(username, line, c):
    command = '''select * from LISTENERS where openid = ?'''
    stu = list(c.execute(command, (username,)))
    if len(stu) != 1:   # 新建一个用户的情况
        command = '''insert into LISTENERS (openid,put_count,get_count) values (?,0,1)'''
        c.execute(command, (username,))
        c.execute('commit;')
    else:   # stu是已有用户
        if stu[0][1] == 5 and stu[0][2] == 0:
            return'达到次数上限啦，请安利至少一首歌再捞歌哟。'
        else:
            command = '''update LISTENERS set get_count = get_count + 1 where openid = ?'''
            c.execute(command, (username,))
            c.execute('commit;')    # 成功捞歌，已捞次数++
    import random
    command = '''select song from SONG'''
    song_list = list(c.execute(command,))
    lucky_song = random.choice(song_list)
    command = '''select recommend from SONG where song = ?'''
    lucky_recommend = list(c.execute(command, (lucky_song[0],)))
    content = lucky_song[0]
    for x in lucky_recommend:
        content += '\n'+x[0]
    return content


def recommend_song(username, line, c):
    command = '''select * from LISTENERS where openid = ?'''
    stu = list(c.execute(command, (username,)))
    if len(stu) != 1:   # 新用户
        command = '''insert into LISTENERS (openid,put_count,get_count) values (?,1,0)'''
        c.execute(command, (username,))
        c.execute('commit;')
    else:   # 用户已存在
        command = '''update LISTENERS set put_count = put_count + 1 where openid = ?'''
        c.execute(command, (username,))
        c.execute('commit;')
    # 找到书名号 把歌名抠出来
    i = 0
    exist = 0
    for x in line:
        if x.find('》') != -1:
            exist = 1
            break
        else:
            i = i+1
    if exist == 0:
        return '对不起，歌名格式有误，请检查是否加了书名号。'
    songname = " ".join(line[1:i+1])
    content = " ".join(line[i+1:])
    # 判不同
    command = '''select * from SONG where song = ? and recommend = ?'''
    rec = list(c.execute(command, (songname, content)))
    if len(rec) == 1:
        return '请不要重复安利哟。'
    command = '''insert into SONG (song,recommend) values (?,?)'''
    c.execute(command, (songname, content))
    c.execute('commit;')
    return '安利成功。:)'


if __name__ == '__main__':
    pass
