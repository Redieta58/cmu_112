from cmu_112_graphics import *

import pygame
import webbrowser
import random
import math
#rlemma
#rediet lemma
def appStarted(app):
    pygame.mixer.init()
    app.backgroundMusic = pygame.mixer.Sound(r'C:\Users\Hp\Downloads\mixkit-game-level-music-689.wav')
    app.backgroundMusic.play(loops=-1)
    
    # Load sound effects
    app.enemyCollisionSound = pygame.mixer.Sound(r'C:\Users\Hp\Downloads\mixkit-apartment-buzzer-bell-press-932.wav')
    app.coinCollectionSound = pygame.mixer.Sound(r'C:\Users\Hp\Downloads\mixkit-retro-game-notification-212.wav')
    
    app.deathCount=0
    app.isPaused =False
    app.topEnemy= {"x": 160,"y":120,"dx":0,"dy": 20}
    app.bottomEnemy = {"x": 120, "y":320, "dx": 0, "dy": 20}
    app.radius = 10
    app.coin= {"x": 520, "y": 160}
    app.board=[
        [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
        [1, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1],
        [1, 0, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1],
        [1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1],
        [1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 1, 1],
        [1, 0, 1, 0, 1, 0, 0, 0, 0, 1, 1, 1],
    ]
    app.playerX= 80
    app.playerY= 240
    app.margin=100
    app.size= 40
    app.dx=40
    app.dy=40
    app.level= 1
    app.isTimeOver=False
    app.timerDelay=1000
    app.score=0
    app.death=0
    app.gameState= "welcome"
    app.color= (0, 177, 255)
################################################################################################



###############################################################################################
def keyPressed(app, event):
    
    borderRight= app.margin + 12*app.size - app.size/2
    borderBottom= app.margin + 6*app.size - app.size/2 
    borderLeft = app.margin + app.size/2 
    borderTop = app.margin + app.size/2
    
    safeLeft= app.margin-2*app.size + + app.size/2 
    safeTop= app.margin + 2*app.size + app.size/2
    safeRight= app.margin + app.size/2
    safeBottom= app.margin + 4*app.size - app.size/2

    safe1Left= app.margin + 12*app.size - app.size/2
    safe1Top= app.margin + 2*app.size + app.size/2
    safe1Right= app.margin + 14*app.size - app.size/2 
    safe1Bottom= app.margin + 4*app.size - app.size/2

########################################################################################
    #Movements
    if event.key == "p" and app.gameState == "play1":
        print(app.isPaused)
        app.isPaused= not app.isPaused
    if not app.isPaused:
        if event.key == "Up":
            newY = app.playerY - app.size
            if isPlayerInArea1(app):
                if newY < borderTop:
                    app.playerY = borderTop
                else:
                    app.playerY = newY
            if isPlayerInArea2(app):
                app.playerY = newY
            if isPlayerInArea3(app):
                app.playerY = newY
            if isPlayerInSafeZone(app):
                if newY < safeTop:
                    app.playerY =safeTop
                else:
                    app.playerY = newY
            if isPlayerInSafeZone1(app):
                if newY < safe1Top:
                    app.playerY = safe1Top
                else:
                    app.playerY= newY
                
        if event.key == "Down":
            newY= app.playerY + app.size
            if isPlayerInArea1(app):
                app.playerY = newY
            if isPlayerInArea2(app):
                app.playerY = newY
            if isPlayerInArea3(app):
                if newY > borderBottom:
                    app.playerY = borderBottom
                else:
                    app.playerY = newY
            if isPlayerInSafeZone(app):
                if newY > safeBottom:
                    app.playerY =safeBottom
                else:
                    app.playerY = newY
            if isPlayerInSafeZone1(app):
                if newY > safe1Bottom:
                    app.playerY = safe1Bottom
                else:
                    app.playerY= newY
    
        if event.key=="Left":
            newX = app.playerX - app.size
            if isPlayerInArea1(app):
                if newX < borderLeft:
                    app.playerX = borderLeft
                else:
                    app.playerX = newX
            if isPlayerInArea2(app):
                app.playerX = newX
            if isPlayerInArea3(app):
                if newX < borderLeft:
                    app.playerX = borderLeft
                else:
                    app.playerX = newX
            if isPlayerInSafeZone(app):
                if newX < safeLeft:
                    app.playerX = safeLeft
                else:
                    app.playerX = newX
            if isPlayerInSafeZone1(app):
                app.playerX = newX
            
            
        if event.key == "Right":
            newX = app.playerX + app.size
            if isPlayerInArea1(app):
                if newX > borderRight:
                    app.playerX = borderRight
                else:
                    app.playerX = newX
            if isPlayerInArea2(app):
                app.playerX = newX
            if isPlayerInArea3(app):
                if newX > borderRight:
                    app.playerX=borderRight
                else:
                    app.playerX = newX
            if isPlayerInSafeZone(app):
                app.playerX = newX
            if isPlayerInSafeZone1(app):
                if newX > safe1Right:
                    app.playerX = safe1Right
                else:
                    app.playerX = newX
##############################################################################################
                #collision Detection
        
    if "x" and "y" in app.coin:
        if detectCollisionWithCoin(app, app.playerX, app.playerY, app.coin):
            app.coinCollectionSound.play()

            del app.coin["x"]
            del app.coin["y"]
            app.gameState = "pass"


    if collisionWithEnemy(app, app.playerX, app.playerY, app.topEnemy) or collisionWithEnemy(app, app.playerX, app.playerY, app.bottomEnemy):
        app.enemyCollisionSound.play()
        app.deathCount += 1
        app.gameState = "play1"
        resetPlayerAndEnemyPosition(app)
        resetCoinPosition(app)
    
    if app.gameState=="pass":
        if isPlayerInSafeZone1(app):
            app.gameState = "win"

            
###############################################################################################



#############################################################################################
def detectCollisionWithCoin(app, playerX, playerY, coin):
    """
    Returns True if the square and circle intersect, False otherwise
    """
    cx= coin["x"]
    recRadius= app.size//2
    cy= coin["y"]
    d= ((playerX - cx)**2 +(playerY - cy)**2)**0.5
    if d<= app.radius + recRadius:
       return True
    else:
        return False

#############################################################################################
def isPlayerInArea1(app):
    
    borderRight= app.margin + 12*app.size - app.size/2
    borderLeft = app.margin + app.size/2 
    borderTop = app.margin + app.size/2
    safeTop= app.margin + 2*app.size + app.size/2

    
    if (borderLeft <= app.playerX <= borderRight) and (borderTop <= app.playerY <= safeTop):
        return True
    else:
        False
        
def isPlayerInArea2(app):
    
    borderLeft = app.margin + app.size/2
    safeTop= app.margin + 2*app.size + app.size/2
    borderRight= app.margin + 12*app.size - app.size/2
    safeBottom= app.margin + 4*app.size - app.size/2
    
    if borderLeft <= app.playerX <= borderRight and safeTop <= app.playerY <= safeBottom:
        return True
    else:
        return False
        
    
def isPlayerInArea3(app):
    
    borderLeft = app.margin + app.size/2
    borderRight= app.margin + 12*app.size - app.size/2
    safeBottom= app.margin + 4*app.size - app.size/2
    borderBottom= app.margin + 6*app.size - app.size/2
    
    if borderLeft <= app.playerX <= borderRight and safeBottom <= app.playerY <= borderBottom:
        return True
    else:
        return False
    
def isPlayerInSafeZone(app):
    
    safeLeft= app.margin-2*app.size  + app.size/2 
    safeTop= app.margin + 2*app.size + app.size/2
    safeRight= app.margin + app.size/2
    safeBottom= app.margin + 4*app.size - app.size/2
    
    if safeLeft <= app.playerX <= safeRight and safeTop <=app.playerY <= safeBottom:
        return True
    else:
        return False
    
def isPlayerInSafeZone1(app):
    
    safe1Left= app.margin + 12*app.size - app.size/2
    safe1Top= app.margin + 2*app.size + app.size/2
    safe1Right= app.margin + 14*app.size - app.size/2 
    safe1Bottom= app.margin + 4*app.size - app.size/2
    
    if safe1Left <= app.playerX <=safe1Right and safe1Top <= app.playerY <= safe1Bottom:
        return True
    else:
        return False
    
##############################################################################################

##############################################################################################




################################################################################################
def timerFired(app):
    print (app.isPaused)
    if not app.isPaused:
        borderBottom = app.margin + 5*app.size
        if app.topEnemy["y"] + app.radius >= borderBottom:
            app.topEnemy["dy"] = -app.topEnemy["dy"]
        app.topEnemy["y"] += app.topEnemy["dy"]
        if app.topEnemy ["y"] <= app.margin:
            app.topEnemy["dy"] = -app.topEnemy["dy"]
        app.topEnemy["y"] += app.topEnemy["dy"]
    
        if app.bottomEnemy["y"] + app.radius >= borderBottom:
            app.bottomEnemy["dy"] = -app.bottomEnemy["dy"]
        app.bottomEnemy["y"] += app.bottomEnemy["dy"]
        if app.bottomEnemy ["y"] <= app.margin:
            app.bottomEnemy["dy"] = -app.bottomEnemy["dy"]
        app.bottomEnemy["y"] += app.bottomEnemy["dy"]
    
    if collisionWithEnemy(app, app.playerX, app.playerY, app.topEnemy) or collisionWithEnemy(app, app.playerX, app.playerY, app.bottomEnemy):
        app.enemyCollisionSound.play()
        if app.gameState== "play1":
            app.deathCount += 1
            resetPlayerAndEnemyPosition(app)
            resetCoinPosition(app)
        if app.gameState == "pass":
            app.deathCount += 1
            app.gameState = "play1"
            resetPlayerAndEnemyPosition(app)
            resetCoinPosition(app)



    


############################################################################################### 
    

##############################################################################################
def collisionWithEnemy(app, playerX, playerY, enemy):
    """
    Returns True if the square and circle intersect, False otherwise
    """
    recRadius= app.size//2
    
    cx1= enemy["x"]
    cy1= enemy["y"]
    cx2= enemy["x"] +2*app.size
    cx3= enemy["x"] +4*app.size
    cx4=enemy["x"] +6*app.size
    cx5= enemy["x"] +8*app.size
    cx6= enemy["x"] +10*app.size
    

    
    d1= ((playerX - cx1)**2 +(playerY - cy1)**2)**0.5
    d2= ((playerX - cx2)**2 +(playerY - cy1)**2)**0.5
    d3=((playerX - cx3)**2 +(playerY - cy1)**2)**0.5
    d4= ((playerX - cx4)**2 +(playerY - cy1)**2)**0.5
    d5= ((playerX - cx5)**2 +(playerY - cy1)**2)**0.5
    d6= ((playerX - cx6)**2 +(playerY - cy1)**2)**0.5

    L= [d1, d2, d3, d4, d5, d6]
    for d in L:
        if d <app.radius + recRadius:
            return True
    return False

################################################################################################
def isMouseInShare(app, x, y):
    
    recLeft = 7*app.width//10
    recTop = 6*app.height//10
    recRight= 9.5*app.width//10
    recBottom= 8*app.height//10
    
    if recLeft <= x <= recRight and recTop <= y <= recBottom:
        return True
    else:
        return False
    
def isMouseInPlay(app, x, y):
    
    playLeft = app.width//3
    playTop = 3*app.height//10
    playRight= 2*app.width//3
    playBottom = 2*app.height//4
    
    if playLeft <= x <= playRight and playTop <= y <= playBottom:
        return True
    else:
        return False
    
def isMouseInRate(app, x, y):
    
    recLeft = app.margin //2
    recTop = 6*app.height//10
    recRight = 2*app.width //7
    recBottom = 8*app.height//10
    
    if recLeft <= x <= recRight and recTop <= y <=recBottom:
        return True
    else:
        return False
def isMouseInMoreGames(app, x, y):
    
    recLeft = app.width//3
    recTop= 6*app.height//10
    recRight = 2*app.width//3
    recBottom = 8*app.height//10
    
    if recLeft <= x <= recRight and recTop <= y <= recBottom:
        return True
    else:
        False

################################################################################################
def isMouseInNextLevel(app, x, y):
    
    recLeft = 7*app.width//10
    recTop = 6*app.height//10
    recRight= 9.5*app.width//10
    recBottom= 8*app.height//10
    
    if recLeft <= x <= recRight and recTop <= y <= recBottom:
        return True
    else:
        return False
    
def isMouseInReplay(app, x, y):
    
    recLeft = app.margin //2
    recTop = 6*app.height//10
    recRight = 2*app.width //7
    recBottom = 8*app.height//10
    
    if recLeft <= x <= recRight and recTop <= y <=recBottom:
        return True
    else:
        return False
def isMouseInLevelSelect(app, x, y):
    
    recLeft = app.width//3
    recTop= 6*app.height//10
    recRight = 2*app.width//3
    recBottom = 8*app.height//10
    
    if recLeft <= x <= recRight and recTop <= y <= recBottom:
        return True
    else:
        False
        
#####################################################################################
def isMouseInLevel1(app, x, y):
    
    recLeft = app.margin
    recTop = 2*app.margin
    recRight= app.width//3
    recBottom =1.5*app.height//2.5
    
    if recLeft <= x <=recRight and recTop <= y <= recBottom:
        return True
    else:
        return False
def isMouseInMainMenu(app, x, y):
    
    recLeft =4*app.width//10
    recTop = app.height - 2*app.height//10
    recRight= 6*app.width//10
    recBottom= app.height - app.height//10
    
    if recLeft <= x <= recRight and recTop <= y <= recBottom:
        return True
    else:
        return False

def isMouseInMenu(app, x, y):
    
    recLeft= 660
    recTop = 0
    recRight= 760
    recBottom= app.margin//2
    
    if recLeft <= x <=recRight and recTop<= y <= recBottom:
        return True
    else:
        return False
###############################################################################################
def mousePressed(app, event):
    
    if app.gameState == "welcome":
        if isMouseInPlay(app, event.x, event.y):
            app.gameState = "chooseLeveL"
    if isMouseInMoreGames(app, event.x, event.y):
        print("mouse in more games")
    if isMouseInRate(app, event.x, event.y):
        print("mouse is rate")
    if isMouseInShare(app, event.x, event.y):
        print("mouse is in share")
    if isMouseInLevel1(app, event.x, event.y):
        app.gameState ="play1"
    if app.gameState=="win":
        if isMouseInReplay(app, event.x, event.y):
            app.deathCount=0
            resetPlayerAndEnemyPosition(app)
            resetCoinPosition(app)
            app.gameState = "play1"
        if isMouseInLevelSelect(app, event.x, event.y):
            app.gameState = "chooseLeveL"
        if isMouseInNextLevel(app, event.x, event.y):
            app.gameState = "play2"
    if app.gameState== "chooseLeveL":
        if isMouseInMainMenu(app, event.x, event.y):
            app.gameState= "welcome"
    if app.gameState == "play1":
        if isMouseInMenu(app, event.x, event.y):
            app.deathCount = 0
            app.gameState= "welcome"
    if app.gameState == "welcome":
        if isMouseInRate(app, event.x, event.y):
            playstore_link = "https://play.google.com/store/apps/details?id=com.cvtgames.Box.Challenge.JK"
            webbrowser.open(playstore_link)
        if isMouseInMoreGames(app, event.x, event.y):
            playstore_link = "https://play.google.com/store/search?q=pub%3ACVT%20Games&c=apps"
            webbrowser.open(playstore_link)
            
    if app.gameState== "pass":
        if isMouseInMenu(app, event.x, event.y):
            app.gameState= "chooseLeveL"
    if app.gameState== "play1":
        if isMouseInMenu(app, event.x, event.y):
            app.gameState = "chooseLeveL"
    if app.gameState=="chooseLeveL":
        resetPlayerAndEnemyPosition(app)
        resetCoinPosition(app)

##############################################################################################
def resetCoinPosition(app):
    app.coin= {"x": 520, "y": 160}
###############################################################################################
def resetPlayerAndEnemyPosition(app):

    app.playerX= 80
    app.playerY= 240
    app.topEnemy= {"x": 160,"y":120,"dx":0,"dy": 20}
    app.bottomEnemy = {"x": 120, "y":320, "dx": 0, "dy": 20}
##################################################################################################

def drawWelcomeScreen(app, canvas):
    textColor =  "#%02x%02x%02x" % (0, 51, 102)
    mainScreenFill = "#%02x%02x%02x" % (204, 204, 255)
    color =  "#%02x%02x%02x" % (255, 204, 255)
    canvas.create_rectangle(0,0,app.width, app.height, fill= mainScreenFill)
    canvas.create_text(app.width//2, app.margin//2, text="BOX CHALLENGE", fill=textColor,
                       font= f'Helvetica 52 bold')
    canvas.create_rectangle(app.width//3, 3*app.height//10, 2*app.width//3, 2*app.height//4, fill= color)
    #create text play
    canvas.create_text(app.width//3 + app.width//6, 4*app.height//10, text= "PLAY", fill=textColor,
                      font = f'Helvetica 40 bold')
    canvas.create_rectangle(app.width//3, 6*app.height//10, 2*app.width//3, 8*app.height//10, fill=color)
    #create text more game
    canvas.create_text(app.width//3 + app.width//6, 7*app.height//10, text="More Games", fill=textColor,
                       font = f'Helvetica 30 bold')
    canvas.create_rectangle(app.margin//2, 6*app.height//10, 2*app.width//7, 8*app.height//10, fill=color)
    #create text rate
    canvas.create_text(1.7*app.width//10, 7*app.height//10, text= "Rate", fill= textColor,
                       font = f'Helvetica 30 bold')
    canvas.create_rectangle(7*app.width//10, 6*app.height//10, 9.5*app.width//10, 8*app.height//10, fill=color)
    #create text share
    canvas.create_text (app.width - 1.7*app.width//10,7*app.height//10, text= "Share", fill= textColor,
                       font = f'Helvetica 30 bold')
    #draw music icon in the right hand corner
    #draw silent form in the right hand corner
######################################################################################################    
def drawSecondScreen(app, canvas):
    mainScreenFill = "#%02x%02x%02x" % (204, 204, 255)
    textColor =  "#%02x%02x%02x" % (0, 51, 102)
    color =  "#%02x%02x%02x" % (255, 204, 255)
    canvas.create_rectangle(0,0,app.width, app.height, fill= mainScreenFill)
    canvas.create_text(app.width//2, app.height//6, text="BOX CHALLENGE", fill=textColor,
                       font= f'Helvetica 52 bold')
    #drawlevels in a locked forms
    canvas.create_rectangle (app.margin, 2*app.margin, app.width//3, 1.5*app.height//2.5, fill=color)
    canvas.create_text(2*app.width//8.5, app.height//2, text= "Level 1", fill=textColor,
                       font= f'Helvetica 30 bold')
    canvas.create_rectangle(4*app.width//10, app.height - 2*app.height//10, 6*app.width//10, app.height - app.height//10, fill= color)
    canvas.create_text(app.width//2, app.height - 1.5*app.height//10, text ="MAIN MENU", fill=textColor,
                       font=  f'Helvetica 15 bold')
    #draw main menu rectangle

def drawLevel1(app, canvas, width, height, board, color):
    recColor =  "#%02x%02x%02x" % (255, 204, 255)
    mainScreenFill = "#%02x%02x%02x" % (178, 102, 255)
    textColor =  "#%02x%02x%02x" % (0, 51, 102)   
    canvas.create_rectangle(0,0,app.width, app.height, fill=mainScreenFill)
    canvas.create_rectangle(0,0, app.margin, app.margin//2)
    canvas.create_text(app.margin//2, app.margin//4, text = "Level 1", fill= textColor,
                       font=  f'Helvetica 15 bold')
    canvas.create_rectangle(660, 0, 760, app.margin//2)
    canvas.create_text(710, app.margin//4, text = f'Menu', fill= textColor, 
                       font=  f'Helvetica 15 bold')
    canvas.create_rectangle(560, 0, 660, app.margin//2)
    canvas.create_text(610, app.margin//4, text = f'Death: {app.deathCount}', fill= textColor,
                            font=  f'Helvetica 15 bold')
    canvas.create_rectangle(100, 390, 580, 490, fill= recColor)
    canvas.create_text(340, 440, text = f'The challenge is to: Collect the coin \n                                Avoid enemies \n                                Move to the safe zone \n Press the arrow keys to move the player. \n You can press "p" to pause or unpause.',
                       font = f'Helvetica 10 bold', fill= textColor)
    
    def colorLightner(color, row, col):
        #create a function that lightens the given color
        r, g, b = color #parallel assignment of r, g, b
        #to find 3 values out of the given color
        if (row % 2 == 0 and col % 2 != 0) or (row % 2 != 0 and col % 2 == 0):
            #each cell with even row and odd column
            #or odd row and even column, then the cell is colored
            r = min(255, r + 100) #add a constant number and cap it at 255
            g = min(255, g + 100)
            b = min(255, b + 100)
        else: #else keep it white
            r=255
            g=255
            b=255
        return "#%02x%02x%02x" % (r, g, b) #change the colors and citation
    #taken from the note
    for row in range(6): #for row in range of 8
        for col in range(12): #for col in range of 8
            cellColor = colorLightner(color, row, col) #takes in 3 parameters
            x0 = app.margin + col * app.size #the starting X point of each cell
            #is the margin plus the number of column * the width of the cell
            y0 = app.margin + row * app.size #the starting Y point of each cell
            #is the margin plus the number of row times cell hieight
            x1 = x0 + app.size #the ending X value is x + width of each cell
            y1 = y0 + app.size #the ending X value is y + height of each cell
            canvas.create_rectangle(x0, y0, x1, y1, fill=cellColor, width=0)
            #create the cells
def drawSafeZone(app, canvas):
    recColor= "#%02x%02x%02x" % (102, 255, 178)
    safeLeft= app.margin-2*app.size
    safeTop= app.margin + 2*app.size
    safeRight= app.margin
    safeBottom= app.margin + 4*app.size

    safe1Left= app.margin + 12*app.size
    safe1Top= app.margin + 2*app.size
    safe1Right= app.margin + 14*app.size
    safe1Bottom= app.margin + 4*app.size

    canvas.create_rectangle(safeLeft, safeTop, safeRight, safeBottom, fill=recColor)
    canvas.create_rectangle(safe1Left, safe1Top, safe1Right, safe1Bottom, fill=recColor)
def drawPass(app, canvas):
    safe1Left= app.margin + 12*app.size
    safe1Top= app.margin + 2*app.size
    safe1Right= app.margin + 14*app.size
    safe1Bottom= app.margin + 4*app.size
    canvas.create_rectangle(safe1Left, safe1Top, safe1Right, safe1Bottom, fill="yellow")
def drawPlayer(app, canvas):
    color= "#%02x%02x%02x" % (255, 51, 153)
    canvas.create_rectangle(app.playerX - app.size//2 , app.playerY - app.size//2,
                            app.playerX + app.size//2 , app.playerY + app.size//2 , fill=color)
    
def drawEnemy(app, canvas):
    color= "#%02x%02x%02x" % (102, 102, 255)
    for i in range(0,12,2):
        x0= app.topEnemy["x"] - app.radius + i*app.size
        y0= app.topEnemy["y"] - app.radius 
        x1= app.topEnemy["x"] + app.radius + i*app.size
        y1= app.topEnemy["y"] + app.radius
        canvas.create_oval(x0, y0, x1, y1, fill=color)
    for i in range(0,12,2):
        x2=app.bottomEnemy["x"] - app.radius + i*app.size
        y2= app.bottomEnemy["y"] - app.radius
        x3=app.bottomEnemy["x"] + app.radius + i*app.size
        y3=app.bottomEnemy["y"] + app.radius
        canvas.create_oval(x2, y2, x3, y3, fill=color)

        
def drawCoin(app,canvas):
    if "x" and "y" in app.coin:
        coinX= app.coin["x"]
        coinY= app.coin["y"]
        canvas.create_oval(coinX-app.radius, coinY-app.radius, coinX + app.radius, coinY + app.radius, fill="yellow")

def drawWinLevel1(app, canvas):
    color= "#%02x%02x%02x" % (160, 32, 240)
    recColor= "#%02x%02x%02x" % (255, 153, 204)
    canvas.create_rectangle(0, 0, app.width, app.height, fill= color)
    canvas.create_text(app.width//2, app.height//4, text = "Well, You Passed The First Challenge!",
                       font = f'Helvetica 30 bold')
    canvas.create_rectangle(app.width//3, 6*app.height//10, 2*app.width//3, 8*app.height//10, fill=recColor)
    canvas.create_text(app.width//3 + app.width//6, 7*app.height//10, text="Level Select",
                       font = f'Helvetica 25 bold')
    canvas.create_rectangle(app.margin//2, 6*app.height//10, 2*app.width//7, 8*app.height//10, fill=recColor)
    #create text rate
    canvas.create_text(1.7*app.width//10, 7*app.height//10, text= "Replay", fill= "black",
                       font = f'Helvetica 25 bold')
    canvas.create_rectangle(7*app.width//10, 6*app.height//10, 9.5*app.width//10, 8*app.height//10, fill=recColor)
    #create text share
    canvas.create_text (app.width - 1.7*app.width//10,7*app.height//10, text= "Next Level", fill= "black",
                       font = f'Helvetica 25 bold')
   
######################################################################################################    

def redrawAll(app,canvas):
    if app.gameState == "welcome":
        drawWelcomeScreen(app, canvas)
    if app.gameState =="chooseLeveL":
        drawSecondScreen(app, canvas)
    if app.gameState == "play1":
        drawLevel1(app, canvas, app.width, app.height, app.board, app.color)
        drawSafeZone(app, canvas)
        drawPlayer(app, canvas)
        drawEnemy(app, canvas)
        drawCoin(app, canvas)
    if app.gameState == "pass":
        drawLevel1(app, canvas, app.width, app.height, app.board, app.color)
        drawSafeZone(app, canvas)
        drawPlayer(app, canvas)
        drawEnemy(app, canvas)
        drawPass(app, canvas)
    if app.gameState == "win":
        drawWinLevel1(app, canvas)

    
    
runApp(width=760, height=520)





