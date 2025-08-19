#
# npcs-in-css CMake file (based on SourceMod CMake Template)
# Copyright (C) 2024 bezdmn
# Copyright (C) 2024 Michael Pekar
# =============================================================================
#
# This program is free software; you can redistribute it and/or modify it under
# the terms of the GNU General Public License, version 3.0, as published by the
# Free Software Foundation.
#
# This program is distributed in the hope that it will be useful, but WITHOUT
# ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
# FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more
# details.
#
# You should have received a copy of the GNU General Public License along with
# this program.  If not, see <http://www.gnu.org/licenses/>.
#

cmake_minimum_required(VERSION 3.22)
project(npcs-in-css)

### Finding the SourceMod SDK and game SDK
get_filename_component(build_root "." ABSOLUTE)

if(IS_DIRECTORY ${build_root}/metamod-source)
    set(MMS "${build_root}/metamod-source")
    MESSAGE(STATUS "Found MM:Source directory at ${MMS}")
else()
    MESSAGE(FATAL_ERROR "Couldn't find MM:Source at ${build_root}/metamod-source/")
endif()

if(IS_DIRECTORY ${build_root}/sourcemod)
    set(SM "${build_root}/sourcemod")
    MESSAGE(STATUS "Found SourceMod directory at ${SM}")
else()
    MESSAGE(FATAL_ERROR "Couldn't find SourceMod SDK at ${build_root}/sourcemod/")
endif()

if(IS_DIRECTORY ${build_root}/hl2sdk-css)
    set(HL2SDK "${build_root}/hl2sdk-css")
elseif(IS_DIRECTORY ${build_root}/hl2sdk)
    set(HL2SDK "${build_root}/hl2sdk")
else()
    MESSAGE(FATAL_ERROR "Couldn't find any HL2SDK!")
endif()

MESSAGE(STATUS "Found HL2SDK directory at ${HL2SDK}")

if(WIN32)
    set(HL2LIB ${HL2SDK}/lib/public)
    error(fixme)
else()
    set(HL2LIB ${HL2SDK}/lib/linux)
    file(GLOB SHARED_LIBS
        "${HL2LIB}/*.a"
        "${HL2LIB}/*.so"
    )
endif()

link_libraries(${SHARED_LIBS})

if(WIN32)
else()
    add_definitions(-DCOMPILER_GCC -DGNUC -DHAVE_STDINT_H -DLINUX -D_LINUX -DPOSIX -D_RETAIL)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Dstricmp=strcasecmp -D_stricmp=strcasecmp -D_strnicmp=strncasecmp")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Dstrnicmp=strncasecmp -D_snprintf=snprintf -Dsprintf_s=snprintf")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_vsnprintf=vsnprintf -D_alloca=alloca -Dstrcmpi=strcasecmp -Dstrncpy_s=strncpy")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D__forceinline= -D__thiscall= -D__cdecl= -D__fastcall= -Dcdecl=")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DMAX_PATH=PATH_MAX")

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pipe -fPIC")

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Werror=return-type -Wno-uninitialized -Wno-switch -Wno-unused -Wno-unused-result")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-non-virtual-dtor -Wno-overloaded-virtual -Wno-implicit-const-int-float-conversion")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-conversion-null -Wno-write-strings -Wno-inconsistent-missing-override -Wno-c++11-narrowing")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-invalid-offsetof -Wno-reorder -Wno-implicit-exception-spec-mismatch -Wno-undefined-bool-conversion")

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse -fno-strict-aliasing -m32 -std=c++17")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-threadsafe-statics -fvisibility=hidden -fvisibility-inlines-hidden")

    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -m32")

    #add_link_options("-Wl,--no-undefined")
    add_link_options("-static-libstdc++")
    add_link_options("-static-libgcc")
endif()

add_definitions(-DSOURCE_ENGINE=5)

### Add source files
add_library(${PROJECT_NAME}
    SHARED
    extension.cpp
    ${SM}/public/smsdk_ext.cpp
    asm/asm.c
    CDetour/detours.cpp
    patch.cpp
    sign_func.cpp
    command.cpp
    CEntity/CE_recipientfilter.cpp
    CEntity/CE_recipientfilter.h
    CEntity/CECollisionProperty.cpp
    CEntity/CEntity.cpp
    CEntity/CEntityBase.cpp
    CEntity/CEntityManager.cpp
    CEntity/CTakeDamageInfo.cpp
    CEntity/cutil.cpp
    CEntity/EntityOutput.cpp
    CEntity/gamemovement.h
    CEntity/GameSystem.cpp
    CEntity/physics.cpp
    CEntity/physics_impact_damage.cpp
    CEntity/rtti.cpp
    CEntity/rtti.h
    CEntity/vtablehook.cpp
    CEntity/stuff/ammodef.cpp
    CEntity/stuff/animation.cpp
    CEntity/stuff/bone_setup.cpp
    CEntity/stuff/eventqueue.cpp
    CEntity/stuff/eventqueue.h
    CEntity/stuff/stringregistry.cpp
    CEntity/stuff/stringregistry.h
    CEntity/stuff/studio.cpp
    CEntity/stuff/TemplateEntities.cpp
    CEntity/stuff/TemplateEntities.h
    CEntity/stuff/globalstate.cpp
    CEntity/stuff/globalstate.h
    CEntity/modify/simtimer.cpp
    CEntity/modify/mempool.h
    CEntity/modify/mempool.cpp
    CEntity/modify/vehicle_viewblend_shared.cpp
    CEntity/modify/fourwheelvehiclephysics.cpp
    CEntity/modify/fourwheelvehiclephysics.h
    CEntity/Class/CAI_NPC.cpp
    CEntity/Class/CAI_relationship.cpp
    CEntity/Class/CAnimating.cpp
    CEntity/Class/CAnimatingOverlay.cpp
    CEntity/Class/CBeam.cpp
    CEntity/Class/CBeam.h
    CEntity/Class/CBreakable.cpp
    CEntity/Class/CBreakable.h
    CEntity/Class/CCombatCharacter.cpp
    CEntity/Class/CCombatWeapon.cpp
    CEntity/Class/CCombatWeapon.h
    CEntity/Class/CCommand.cpp
    CEntity/Class/CCommand.h
    CEntity/Class/CCycler_Fix.cpp
    CEntity/Class/CCycler_Fix.h
    CEntity/Class/CDoor.cpp
    CEntity/Class/CDoor.h
    CEntity/Class/CDynamicLink.cpp
    CEntity/Class/CDynamicLink.h
    CEntity/Class/CDynamicProp.cpp
    CEntity/Class/CDynamicProp.h
    CEntity/Class/CEAI_ScriptedSequence.cpp
    CEntity/Class/CEnvExplosion.cpp
    CEntity/Class/CEnvExplosion.h
    CEntity/Class/CEnvGunfire.cpp
    CEntity/Class/CEnvGunfire.h
    CEntity/Class/CEnvLaser.cpp
    CEntity/Class/CEnvLaser.h
    CEntity/Class/CESoundEnt.cpp
    CEntity/Class/CESoundEnt.h
    CEntity/Class/CFlex.cpp
    CEntity/Class/CFuncPhysbox.cpp
    CEntity/Class/CFuncPhysbox.h
    CEntity/Class/CGib.cpp
    CEntity/Class/CGib.h
    CEntity/Class/CGrenade.cpp
    CEntity/Class/CGrenade.h
    CEntity/Class/CInfoTarget_Fix.cpp
    CEntity/Class/CInfoTarget_Fix.h
    CEntity/Class/CItem.cpp
    CEntity/Class/CItem.h
    CEntity/Class/CMapRules.cpp
    CEntity/Class/CPhysBox.cpp
    CEntity/Class/CPhysBox.h
    CEntity/Class/CPhysicsProp.cpp
    CEntity/Class/CPhysicsProp.h
    CEntity/Class/CPlayer.cpp
    CEntity/Class/CProp.cpp
    CEntity/Class/CProp.h
    CEntity/Class/CPropDoor.cpp
    CEntity/Class/CPropDoor.h
    CEntity/Class/CRagdollProp.cpp
    CEntity/Class/CRagdollProp.h
    CEntity/Class/CSimTimer.h
    CEntity/Class/CSkyCamera.cpp
    CEntity/Class/CSmoke_trail.cpp
    CEntity/Class/CSmoke_trail.h
    CEntity/Class/CSode_Fix.cpp
    CEntity/Class/CSode_Fix.h
    CEntity/Class/CSpotlightEnd.cpp
    CEntity/Class/CSpotlightEnd.h
    CEntity/Class/CSprite.cpp
    CEntity/Class/CSprite.h
    CEntity/Class/CSpriteTrail.cpp
    CEntity/Class/CSpriteTrail.h
    CEntity/Class/CToggle.cpp
    CEntity/Class/CToggle.h
    CEntity/Class/CTrigger.cpp
    CEntity/Class/CTrigger.h
    CEntity/Class/CTriggerMultiple.cpp
    CEntity/Class/CTriggerMultiple.h
    CEntity/Class/CViewModel.cpp
    CEntity/Class/CViewModel.h
    CEntity/Class/CWorld.cpp
    CEntity/Class/CWorld.h
    CEntity/Class/ItemRespawnSystem.cpp
    CEntity/Class/ItemRespawnSystem.h
    CEntity/Class/CAI_FollowGoal_Fix.cpp
    CEntity/Class/CAI_FollowGoal_Fix.h
    CEntity/Class/CAI_NPC_AI_Patch.cpp
    CEntity/Class/CAI_NPC_AI_Patch.h
    CEntity/Class/CNPCBaseWeapon.cpp
    CEntity/Class/CNPCBaseWeapon.h
    CEntity/Class/CGrenadeWeapon.cpp
    CEntity/Class/CGrenadeWeapon.h
    CEntity/Class/CBoneFollower.cpp
    CEntity/Class/CBoneFollower.h
    CEntity/Class/CBreakableProp.cpp
    CEntity/Class/CBreakableProp.h
    CEntity/Class/CFilters.cpp
    CEntity/Class/CFilters.h
    CEntity/Class/CEntityFlame.cpp
    CEntity/Class/CEntityFlame.h
    CEntity/Class/CPathTrack.cpp
    CEntity/Class/CParticleEntity.cpp
    CEntity/Class/CEnvPlayerSurfaceTrigger.cpp
    CEntity/Class/CEnvPlayerSurfaceTrigger.h
    CEntity/Class/CEntityBlocker.cpp
    CEntity/Class/CEntityBlocker.h
    CEntity/Class/CPropVehicle.cpp
    CEntity/Class/CPropVehicle.h
    CEntity/AI/activitylist.cpp
    CEntity/AI/CAI_baseactor.cpp
    CEntity/AI/CAI_baseactor.h
    CEntity/AI/CAI_basehumanoid.cpp
    CEntity/AI/CAI_basehumanoid.h
    CEntity/AI/CAI_basenpc_physicsflyer.cpp
    CEntity/AI/CAI_basenpc_physicsflyer.h
    CEntity/AI/CAI_behavior.cpp
    CEntity/AI/CAI_Behavior.h
    CEntity/AI/CAI_behavior_assault.cpp
    CEntity/AI/CAI_behavior_assault.h
    CEntity/AI/CAI_behavior_follow.cpp
    CEntity/AI/CAI_behavior_follow.h
    CEntity/AI/CAI_behavior_rappel.cpp
    CEntity/AI/CAI_behavior_rappel.h
    CEntity/AI/CAI_behavior_standoff.cpp
    CEntity/AI/CAI_behavior_standoff.h
    CEntity/AI/CAI_Blended_Movement.cpp
    CEntity/AI/CAI_Blended_Movement.h
    CEntity/AI/CAI_Criteria.cpp
    CEntity/AI/CAI_Default.cpp
    CEntity/AI/CAI_goalentity.cpp
    CEntity/AI/CAI_goalentity.h
    CEntity/AI/CAI_Hint.cpp
    CEntity/AI/CAI_HULL.cpp
    CEntity/AI/CAI_Interest_Target.cpp
    CEntity/AI/CAI_Link.cpp
    CEntity/AI/CAI_Link.h
    CEntity/AI/CAI_LocalIdSpace.cpp
    CEntity/AI/CAI_localnavigator.cpp
    CEntity/AI/CAI_Memory.cpp
    CEntity/AI/CAI_Motor.cpp
    CEntity/AI/CAI_Moveprobe.cpp
    CEntity/AI/CAI_Moveshoot.cpp
    CEntity/AI/CAI_Movesolver.cpp
    CEntity/AI/CAI_Navigator.cpp
    CEntity/AI/CAI_Network.cpp
    CEntity/AI/CAI_NetworkManager.cpp
    CEntity/AI/CAI_Node.cpp
    CEntity/AI/CAI_Node.h
    CEntity/AI/CAI_pathfinder.cpp
    CEntity/AI/CAI_planesolver.h
    CEntity/AI/CAI_ResponseSystem.cpp
    CEntity/AI/CAI_Route.cpp
    CEntity/AI/CAI_Route.h
    CEntity/AI/CAI_Schedule.cpp
    CEntity/AI/CAI_senses.cpp
    CEntity/AI/CAI_Sentence.cpp
    CEntity/AI/CAI_Sentence.h
    CEntity/AI/CAI_speech.cpp
    CEntity/AI/CAI_Squad.cpp
    CEntity/AI/CAI_tacticalservices.cpp
    CEntity/AI/CAI_tacticalservices.h
    CEntity/AI/CAI_utils.cpp
    CEntity/AI/CAI_utils.h
    CEntity/AI/CAI_waypoint.cpp
    CEntity/AI/CH_AI.cpp
    CEntity/AI/choreoevent.cpp
    CEntity/AI/choreoscene.cpp
    CEntity/AI/eventlist.cpp
    CEntity/AI/interpolatortypes.cpp
    CEntity/AI/sceneentity.cpp
    CEntity/AI/CAI_planesolver.cpp
    CEntity/AI/CAI_behavior_lead.cpp
    CEntity/AI/CAI_behavior_lead.h
    CEntity/AI/CAI_behavior_police.cpp
    CEntity/AI/CAI_behavior_police.h
    CEntity/AI/CAI_goal_police.cpp
    CEntity/AI/CAI_goal_police.h
    CEntity/AI/CAI_playerally.cpp
    CEntity/AI/CAI_playerally.h
    CEntity/AI/CAI_speechfilter.cpp
    CEntity/AI/CAI_speechfilter.h
    CEntity/AI/CAI_behavior_functank.cpp
    CEntity/AI/CAI_behavior_functank.h
    CEntity/AI/CAI_behavior_actbusy.cpp
    CEntity/AI/CAI_behavior_actbusy.h
    CEntity/AI/CAI_spotlight.cpp
    CEntity/AI/CAI_trackpather.cpp
    Custom/env_flare.cpp
    Custom/env_flare.h
    Custom/env_headcrabcanister.cpp
    Custom/env_headcrabcanister_shared.cpp
    Custom/env_headcrabcanister_shared.h
    Custom/func_tank.cpp
    Custom/func_tank.h
    Custom/grenade_beam.cpp
    Custom/grenade_beam.h
    Custom/grenade_frag.cpp
    Custom/grenade_frag.h
    Custom/grenade_spit.cpp
    Custom/grenade_spit.h
    Custom/item_ammo.cpp
    Custom/item_ammo_crate.cpp
    Custom/item_battery.cpp
    Custom/item_healthkit.cpp
    Custom/item_healthkit.h
    Custom/item_recharge.cpp
    Custom/ladder.cpp
    Custom/pcs.cpp
    Custom/pcs.h
    Custom/pickup.cpp
    Custom/player_pickup.cpp
    Custom/player_pickup.h
    Custom/prop_combine_ball.cpp
    Custom/prop_combine_ball.h
    Custom/triggers.cpp
    Custom/weapon_ar2.cpp
    Monster/antlion_maker.cpp
    Monster/antlion_maker.h
    #Monster/maprules.cpp
    Monster/monstermaker.cpp
    Monster/monstermaker.h
    Monster/npc_antlion.cpp
    Monster/npc_antlion.h
    Monster/npc_antlionguard.cpp
    Monster/npc_barnacle.cpp
    Monster/npc_barnacle.h
    Monster/npc_basescanner.cpp
    Monster/npc_basescanner.h
    Monster/npc_basezombie.cpp
    Monster/npc_basezombie.h
    Monster/npc_bullseye.cpp
    Monster/npc_combine.cpp
    Monster/npc_combines.cpp
    Monster/npc_combines.h
    Monster/npc_fastzombie.cpp
    Monster/npc_headcrab.cpp
    Monster/npc_houndeye.cpp
    Monster/npc_houndeye.h
    Monster/npc_manhack.cpp
    Monster/npc_manhack.h
    Monster/npc_PoisonZombie.cpp
    Monster/npc_rollermine.cpp
    Monster/npc_rollermine.h
    Monster/npc_scanner.cpp
    Monster/npc_scanner.h
    Monster/npc_stalker.cpp
    Monster/npc_stalker.h
    Monster/npc_turret_ceiling.cpp
    Monster/npc_turret_floor.cpp
    Monster/npc_turret_floor.h
    Monster/npc_vortigaunt.cpp
    Monster/npc_vortigaunt.h
    Monster/npc_zombie.cpp
    Monster/temp_npc.cpp
    Custom/vehicle_base.cpp
    CEntity/AI/CAI_trackpather.h
    Monster/cbasehelicopter.cpp
    CEntity/stuff/effects.h
    CEntity/stuff/effects.cpp
    Monster/npc_attackchopper.cpp
    CEntity/Class/CSoda_Fix.cpp
    Custom/vehicle_jeep.cpp
    Custom/vehicle_jeep.h
    Monster/npc_crow.cpp
    Monster/npc_crow.h
    Custom/vehicle_prisoner_pod.cpp
    Monster/npc_combinegunship.cpp
    Custom/weapon_replace.cpp
    Custom/weapon_replace.h
    Custom/weapon_pistol.cpp
    Custom/weapon_pistol.h
    Custom/weapon_smg1.cpp
    Custom/weapon_smg1.h
    Custom/weapon_shotgun.cpp
    Custom/weapon_stunstick.cpp
    Custom/weapon_stunstick.h
    Monster/npc_metropolice.cpp
    Monster/npc_metropolice.h
    Custom/weapon_rpg.cpp
    Custom/weapon_rpg.h
    Custom/weapon_rpg_replace.cpp
    Custom/weapon_rpg_replace.h
    Monster/npc_missiledefence.cpp
    Custom/weapon_physcannon_replace.cpp
    Custom/weapon_physcannon_replace.h
    Monster/npc_enemyfinder.cpp
    Custom/combine_mine.cpp
    Custom/combine_mine.h
    Custom/prop_thumper.cpp
    Monster/npc_strider.cpp
    Monster/npc_strider.h
    Custom/cblast.cpp
    Monster/npc_puppet.cpp
    Monster/npc_combinedropship.cpp
)

target_include_directories(${PROJECT_NAME} PUBLIC
    CDetour
    CEntity/modify
    CEntity/stuff
    CEntity/AI
    CEntity/Class
    CEntity
    Custom
    ${HL2SDK}/public
    ${HL2SDK}/public/game/server
    ${HL2SDK}/public/engine
    ${HL2SDK}/public/tier0
    ${HL2SDK}/public/tier1
    ${HL2SDK}/public/tier3
    ${HL2SDK}/public/vstdlib
    ${HL2SDK}/game/shared
    ${HL2SDK}/public/toolframework
    ${HL2SDK}/public/SoundEmitterSystem
    ${HL2SDK}/game/server
    ${HL2SDK}/public/mathlib
    ${MMS}/core/
    ${MMS}/core/sourcehook
    ${SM}/public
    ${SM}/public/extensions
    ${SM}/sourcepawn/include
    ${SM}/public/amtl/amtl
    ${SM}/public/amtl
    Monster
    sdk
    .
)

set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "")

if (NOT "${EXT_DIR}" STREQUAL "")
	add_custom_command(
		TARGET ${PROJECT_NAME} POST_BUILD
		COMMAND ${CMAKE_COMMAND} -E copy
			$<TARGET_FILE:${PROJECT_NAME}>
			"${EXT_DIR}/monster.ext.so"
	)
endif()